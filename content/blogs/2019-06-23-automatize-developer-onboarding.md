---
title: Automatize your new developer's team onboarding with this simple app (in Python)
date: 2019-06-23 09:00:00 +0000
tags: [python,management,onboarding]
published: true
---

When a new developer joins our team, everybody wants to give them the best possible experience 🙌.

![](https://media.giphy.com/media/FQyQEYd0KlYQ/giphy.gif)

We've created a simple command line app in Python that with minimum configuration helps the new developer, in a geeky way, set
up his/her environment and checks if everything works! It also gives information about which services would they have access to
so they can check and don't need to go somewhere else for that info.

We use [Click](https://click.palletsprojects.com/en/7.x/), the library to create Command line interfaces in Python, and YAML for the configuration. The config has a specific structure
moreover, we can configure the file path so someone could reuse the project for their own company 🤓.

[The documentation is in the README](https://github.com/TheNeonProject/onboarding). You can set the company information in the config, as [in our own that you could use as an example](https://github.com/TheNeonProject/onboarding/blob/master/config.yml). To use your config file, you can set `ONBOARDING_FILE_PATH`.

You can install it with [pipenv](https://github.com/pypa/pipenv).

```bash
pip3 install pipenv
pipenv --python 3.7
pipenv install
```

Once this command finishes, we can run it using `pipenv`.

```bash
export ONBOARDING_FILE_PATH=config.yml
pipenv run python cli.py
```

[It has the configuration to launch from VS Code Debug tab too](https://github.com/TheNeonProject/onboarding/blob/master/.vscode/launch.json).

You can see here the output of the app with my company configuration.

![](https://user-images.githubusercontent.com/488556/59706185-3a765680-9200-11e9-90ce-490c377e7016.png)

![](https://user-images.githubusercontent.com/488556/59706184-3a765680-9200-11e9-9a29-a10e7fe8fff1.png)

[The project is open source and can you can clone it from GitHub](https://github.com/TheNeonProject/onboarding).

Maybe if we have time, we'll create a Python package and add more functionality 🎸, but for now, it does the trick. :-)
