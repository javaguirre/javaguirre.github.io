---
title: Infrastructure as code, present and future
date: 2021-05-20 09:00:00 +0000
tags: [TODO]
published: false
---

Infrastructure as code is the present and the future of managing infrastructure. Declarative model and
standarized infrastructure language, version controlled.

```terraform example of wordpress
```

```ECR example```

## My Past

I've been always looking for ways of improving my own workflow, to automatize everything
that wasn't really creative of my work, was repetitive or I couldn't version.

I installed many servers manually on my first two years of geeking and working, later too, but that changed
when I learnt about Chef.

This made me use Puppet a long time ago, but at that point

Whenever I choose a library for anything, the first thing I check is how testing works with that library,
and if the own library has tests, of course.

VERY IMPORTANT Automated testing and link to Ansible tests

I moved fast to Ansible because the files were much easier, the learning curve didn't need to learn a new language.
It seemed the errors were much more straightforward to get and debug, and It's written in Python.

NixOS (repo)

Docker (CI/BUILD/ETC)

Terraform/AWS Lambda

Kubernetes/Helm

## Why

Declare your wishes, version controlled, self documented, Standard, Can be tested by a CI, easier to spot inconsistencies or security flaws.
Non deprecated documentation, what's in there it's what's applied. Less time to spend on managing it.

Easy Control of the infrastructure lifecycle (I delete an entity in the code, I apply it, and It's gone).

There are several alternatives, but we use Terraform.

(Declarative, provider agnostic(AWS, IBM, etc), plan before execute, status on S3, etc)
Plan,Apply,Destroy
