---
title: 'Running Periodic Lambda Jobs with a CLI Command: A Deep Dive (Part 2)'
date: 2024-08-22 10:42:52 +0000
tags: [lambda,aws,python,cdk]
published: true
---

# Running Periodic Lambda Jobs with a CLI Command: A Deep Dive (Part 2)

In [Part 1](#), we explored how to create and run Lambda jobs using a CLI command. Now, let’s take a deeper dive into how AWS Lambda integrates with the AWS CDK (Cloud Development Kit) stack, and how this powerful combination leverages AWS infrastructure to automate job execution. We’ll also discuss how easy it is to create and deploy new jobs using the CDK.

## AWS Infrastructure with CDK and Lambda

When you’re working with AWS services, the AWS CDK provides a high-level, code-centric approach to define your cloud infrastructure. This includes setting up AWS Lambda functions, VPCs, IAM roles, and event-driven triggers like CloudWatch Events (or EventBridge).

### Setting Up the Lambda Function

The heart of our job runner is the Lambda function, which is responsible for executing the specified jobs. In the AWS CDK, this function is defined in the `TaskExecutorStack` class.

Here’s how the Lambda function is set up:

```python
import os
from aws_cdk import aws_ec2, aws_iam, aws_lambda, aws_logs
from myapp.config import Config, SERVICE, AWSPolicyService


def task_executor(
    scope: Construct, config: Config, vpc: aws_ec2.Vpc
) -> aws_lambda.Function:
    _lambda_role = aws_iam.Role(
        scope,
        "task_executor_lambda_role",
        assumed_by=aws_iam.ServicePrincipal(service="lambda"),
        role_name="task-executor-lambda-role",
        managed_policies=[
            aws_iam.ManagedPolicy.from_aws_managed_policy_name(
                "service-role/AWSLambdaBasicExecutionRole"
            ),
            aws_iam.ManagedPolicy.from_aws_managed_policy_name(
                "service-role/AWSLambdaVPCAccessExecutionRole"
            ),
        ],
    )

    AWSPolicyService.add_policies(_lambda_role, policies)

    _lambda = aws_lambda.DockerImageFunction(
        scope,
        "task_executor_lambda",
        code=aws_lambda.DockerImageCode.from_image_asset(
            os.path.join(os.path.dirname(__file__), "./source"),
            file="Dockerfile.task_executor",
        ),
        description="Task Executor Lambda Function",
        function_name=f"XXXX_{config.stage}_task_executor",
        log_retention=aws_logs.RetentionDays.TWO_MONTHS,
        environment={
            "LOG_LEVEL": "INFO",
            "REGION": config.region,
            # Additional environment variables
        },
        timeout=Duration.minutes(5),
        memory_size=256,
        retry_attempts=0,
        role=_lambda_role,
        vpc=vpc,
    )
    return _lambda
```

### Key Components

1. **IAM Role and Policies**:
   - The Lambda function is assigned an IAM role with the necessary permissions to interact with other AWS services (e.g., S3, SQS, Secrets Manager).
   - This role ensures that the function has the right level of access to perform its tasks securely.

2. **VPC Integration**:
   - The Lambda function is deployed within a VPC, allowing it to access resources in a private network, like databases or other internal services.

3. **Docker Image Deployment**:
   - The Lambda function is built and deployed using a Docker image, which provides a consistent and reproducible environment for running jobs. This is especially useful when the job logic requires a specific set of dependencies or a custom runtime.

### Scheduling Jobs with CDK

The CDK also simplifies scheduling jobs using CloudWatch Events (or EventBridge). The `TaskExecutorStack` class contains a list of jobs that need to be scheduled, and the `_schedule_rule` method handles the creation of these schedules.

```python
import json
from aws_cdk import aws_events as events
# ...


class TaskExecutorStack(Stack):
    def __init__(
        self,
        scope: Construct,
        construct_id: str,
        config,
        **kwargs,
    ) -> None:
        super().__init__(scope, construct_id, **kwargs)

        # This is an example, we could have a configuration file
        # or parse these from JSON
        # expression as in AWS Events expression documentation
        jobs = [
            {
                "name": "job1",
                "function_path": "myapp.tasks.job1.run",
                "parameters": {"key": "value"},
                "schedule": "cron(0 12 * * ? *)",
            },
            {
                "name": "job2",
                "function_path": "myapp.tasks.job2.run",
                "parameters": {"key": "value"},
                "schedule": "rate(1 day)",
            },
        ]
        RUNNER_JOBS = list[RunnerJobSchema].parse_obj(jobs)

        vpc = aws_ec2.Vpc.from_lookup(
            self, "vpc", vpc_id=config.api_config.vpc_params.vpc_id
        )

        bucket_arn = f"arn:aws:s3:::XXXX-XXXX-documents"

        policy = aws_iam.PolicyStatement(
            actions=["s3:GetObject", "s3:ListBucket"],
            resources=[bucket_arn, f"{bucket_arn}/*"],
        )

        self.task_executor_lambda = task_executor(self, config, vpc)
        self.task_executor_lambda.add_to_role_policy(policy)

        list(map(self._schedule_rule, RUNNER_JOBS))

    def _schedule_rule(self, job: RunnerJobSchema) -> None:
        rule = events.Rule(
            self,
            job.name,
            schedule=events.Schedule.expression(job.schedule),
        )
        rule.add_target(
            targets.LambdaFunction(
                self.task_executor_lambda,
                event=events.RuleTargetInput.from_object({"job": job.dict()}),
            )
        )
```

### Key Takeaways

- **Ease of Job Creation**: Adding new jobs is as simple as defining a new entry in the `RUNNER_JOBS` list (We could parse from os.env). Each job includes the function path, parameters, and schedule expression (e.g., cron, rate).
- **Infrastructure as Code**: The entire infrastructure setup, from Lambda to IAM roles, is defined in code. This makes it easy to version control, replicate environments, and automate deployments.
- **Event-Driven Execution**: Jobs are scheduled and triggered automatically based on the defined schedule, leveraging AWS's event-driven architecture.

## Deploying the CDK Stack

Once you’ve defined your jobs and infrastructure, deploying it is a breeze with CDK. Here’s how you can deploy your stack:

1. **Bootstrap CDK**:
   If this is your first time deploying a CDK stack in your account, you’ll need to bootstrap your environment:
   ```bash
   cdk bootstrap
   ```

2. **Deploy the Stack**:
   To deploy your `TaskExecutorStack` stack, simply run:
   ```bash
   cdk deploy TaskExecutorStack
   ```

This command will provision all the necessary AWS resources, including the Lambda function, IAM roles, and event rules, ensuring that your jobs are ready to be executed.

## Wrapping It Up

The AWS CDK provides an elegant way to define and deploy your Lambda jobs, leveraging AWS infrastructure to handle everything from IAM roles to VPC configurations. By defining your infrastructure as code, you gain the benefits of automation, reproducibility, and scalability. Adding new jobs or modifying existing ones is straightforward and integrated into a well-structured deployment process.
