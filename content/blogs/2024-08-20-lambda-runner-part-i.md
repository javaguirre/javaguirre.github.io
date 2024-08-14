---
title: 'Running Periodic Lambda Jobs with a CLI Command: A Deep Dive (Part 1)'
date: 2024-08-20 10:42:52 +0000
tags: [lambda,aws,python,cdk]
published: true
---

# Running Periodic Lambda Jobs with a CLI Command: A Deep Dive (Part 1)

When you're working with AWS Lambda and automation, there comes a time when you need to execute jobs programmatically. This is where the power of a custom CLI command shines. In this post, we'll explore how to run Lambda jobs using a Python CLI command, providing flexibility whether you want to run the job locally or on AWS Lambda.

## The CLI Command: `runner`

Let’s break down the CLI command that’s designed to trigger a Lambda function or run the job locally based on your requirements.

### Command Structure

Here’s the core of our CLI command, written with `Click`, a Python package for creating command-line interfaces:

```python
import json
import boto3
import click

from myapp.script_runner.runner import run_job_locally

@cli.command()
@click.option("--stage", required=False, default="dev")
@click.option("--job", required=True)
@click.option("--parameters", required=False)
@click.option("--run-locally", is_flag=True)
def runner(stage, job, parameters, run_locally):
    """
    Run a job in the Runner

    job: namespace of the function to run (Python)
    parameters: JSON string with the properties to pass to the function
    stage: Stage to run the job in (dev, prod, staging)
    run_locally: Run the job locally instead of in AWS Lambda

    Example:
        python manage.py runner --job "myapp.tasks.hello.hello" --parameters '{"name": "Woot"}'
    """
    if run_locally:
        run_job_locally(stage, job, parameters)
        return

    FUNCTION_NAME = f"myapp_{stage}_task_executor"

    session = boto3.Session(region_name=AWS_REGION)
    lambda_client = session.client("lambda")

    click.echo(f"Launching the runner {job}")

    payload = {
        "job": {
            "function_path": job,
            "name": "",
            "schedule": "",
            "parameters": json.loads(parameters) if parameters else {},
        }
    }

    try:
        response = lambda_client.invoke(
            FunctionName=FUNCTION_NAME,
            InvocationType="RequestResponse",
            Payload=json.dumps(payload).encode("utf-8"),
        )

        response_payload = json.loads(response["Payload"].read().decode("utf-8"))
        body: dict = json.loads(response_payload["body"])["result"]

        click.echo(f"\nResponse from Lambda:\n\n{body}\n")
    except Exception as e:
        click.echo(f"Error invoking Lambda: {str(e)}")
```

### How It Works

1. **Options and Parameters**:
   - `--stage`: Specifies the environment stage (e.g., dev, prod, staging).
   - `--job`: The fully qualified name of the Python function to run.
   - `--parameters`: JSON string containing the parameters to pass to the function.
   - `--run-locally`: Flag to determine if the job should run locally.

2. **Local Execution**:
   If the `--run-locally` flag is set, the `run_job_locally` function is invoked. This function dynamically imports the specified module, calls the function with the provided parameters, and displays the result.

3. **AWS Lambda Execution**:
   Without the `--run-locally` flag, the job is executed on AWS Lambda. The Lambda function name is dynamically generated based on the stage, and the job is triggered with the provided parameters.

### Example Usage

You can run the job locally or on AWS Lambda with a simple command, I called it `manage.py` in honor of Django's manage.py that I used extensively in the past:

```bash
# Run locally
python manage.py runner --job "myapp.tasks.report_generator.execute" --run-locally --parameters='{"options": {}}'

# Run on Lambda
python manage.py runner --job "myapp.tasks.report_generator.execute" --parameters='{"options": {}}'
```

## The Runner (and Job) Service

A simple pydantic model and service class are used to define and execute the job. The `RunnerJobSchema` model represents the job to be executed, and the `RunnerService` class processes the job by dynamically importing the module and calling the function.

It imports the necessary modules, validates the job schema, and executes the job by dynamically importing the module and calling the function.

```python
import importlib
from pydantic import BaseModel
from types import ModuleType

from myapp.script_runner.schemas import RunnerJobSchema
from myapp.utils.logger import LoggerFactory

logger = LoggerFactory(__name__)


class RunnerJobSchema(BaseModel):
    name: str
    function_path: str
    parameters: dict
    schedule: str


class RunnerService:
    def run(self, job: RunnerJobSchema):
        module_path, function_name = job.function_path.rsplit(".", 1)

        logger.info(f"Running {function_name} from {module_path}")

        module: ModuleType = importlib.import_module(module_path)
        function = getattr(module, function_name)
        result = function(**job.parameters)

        return result
```


## The Lambda Handler

The corresponding Lambda function for this setup is straightforward. It receives the event, processes the job, and returns the result.

```python
import json
from typing import Any
from myapp.script_runner.schemas import RunnerJobSchema
from myapp.script_runner.services import RunnerService
from myapp.utils.logger import LoggerFactory

logger = LoggerFactory(__name__)

def handle_job(event: dict) -> dict:
    event_job: RunnerJobSchema = RunnerJobSchema.parse_obj(event["job"])

    logger.info(f"Running event {event_job.function_path}")

    result = RunnerService().run(event_job)

    return {
        "statusCode": 200,
        "body": json.dumps({
            "message": "Task executed successfully",
            "result": result,
        })
    }

def lambda_handler(event: dict, context: Any) -> dict:
    return handle_job(event)
```

This Lambda function acts as a handler, processing the job passed to it and returning the results in a structured format.

## Wrapping It Up

The combination of a powerful CLI and AWS Lambda can supercharge your automation processes. Whether you need to run jobs locally for testing or push them to Lambda for production execution, this setup provides a robust and flexible solution.

Remember, with great power comes great responsibility. Use these tools wisely to ensure your tasks are executed efficiently and securely across your environments.

---

In the next part, we’ll dive deeper into the Lambda job execution, explore error handling strategies, and discuss how to extend this CLI for more complex workflows. Stay tuned!
