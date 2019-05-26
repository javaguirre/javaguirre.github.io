---
title: Debug easily Python and Django from Visual Studio Code
date: 2019-05-22 22:21:00 +0000
tags: git
cover: ''
toc: true
published: false
---

Debugging code, sometimes painful, sometimes funny, but always an interesting task. If we don't use the right tools It's where the developer can waste more time.
Making debugging easy for developers have been sometimes a problem for technology, but fortunately in Python there have been good debug options right from the start.

GIF

## TL;DR

From VS Code we can easily debug Python code, even when running inside of a Docker container using ptvsd. I'll tell you how.

## Print

We all have printed code at some point of our career, but the path to become a better developer goes beyond that,
it's very important to spend time researching until we find the better tools for the task.

GIF print

## PDB

PDB and pudb are great tools! I still use them sometimes when I want to debug something tricky, but for debugging speed purposes
I find much more convenient using VS Code Debug tab

## VS Code

I'm gonna use a fairly difficult case, a Django project, but this can be used for any python project.

```
{
    "name": "Django: Runserver",
    "type": "python",
    "request": "launch",
    "program": "${workspaceFolder}/manage.py",
    "args": [
        "runserver",
        "--noreload",
        "--nothreading"
    ],
    "django": true
}
```

```
{
    "name": "Django: Test",
    "type": "python",
    "request": "launch",
    "program": "${workspaceFolder}/manage.py",
    "args": [
        "test"
    ],
    "django": true
}
```

## VS Code debug in Docker

Dockerfile

```
FROM python:3.7

ENV PYTHONDONTWRITEBYTECODE 1
ENV PYTHONUNBUFFERED 1

RUN mkdir /code

WORKDIR /code

RUN pip install pipenv
ADD Pipfile /code/
RUN pipenv install --dev

COPY . /code/

EXPOSE 8000
```

docker-compose.yml

```
version: '3.7'

services:
  db:
    ...

  backend:
    build: .
    image: mydjangoproject
    command: sh ./entrypoint.sh
    environment:
      DJANGO_DEBUG: 1
    volumes:
      - .:/code
    ports:
      - 8000:8000  # Serving Django
      - 8888:8888  # ptvsd service
    depends_on:
      - db

  frontend:
    ...
```

wsgi.py and [ptvsd](https://github.com/microsoft/ptvsd) the Python tools for the Visual Studio debugger

```
import os

from django.core.wsgi import get_wsgi_application


os.environ.setdefault("DJANGO_SETTINGS_MODULE", "myproject.settings")

if os.environ.get('DJANGO_DEBUG', False):  # You can use django.conf settings.DEBUG
    import ptvsd
    ptvsd.enable_attach(address=('0.0.0.0', 8888))
    ptvsd.wait_for_attach()

application = get_wsgi_application()
```

```
{
    "name": "Remote Django App",
    "type": "python",
    "request": "attach",
    "pathMappings": [
        {
            "localRoot": "${workspaceFolder}",
            "remoteRoot": "/code"
        }
    ],
    "port": 8888,
    "host": "localhost"
}
```

Video
