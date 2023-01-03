---
title: Disaster recovery in Heroku for Wealize
date: 2022-06-01 09:00:00 +0000
tags: heroku recovery
published: true
---

## Index

Because things can and will go wrong eventually, we need to have the right way of recovering from it.

When we push to production, and the application fails, the first thing we need to do is roll back to the previous working version so we can debug the problem relaxed and in the warm place of $HOME.

![https://media.giphy.com/media/cT5gKT412jHqw/giphy.gif](https://media.giphy.com/media/cT5gKT412jHqw/giphy.gif)

Given we're using Heroku, it makes it very easy to go back and recover fast, let's document which will be the steps to be back online without any hassles.

## Rollback from web

First things first, we'll go to our Activity view on Heroku. Usually, the URL is <https://dashboard.heroku.com/apps/<app-production>/activity.> We'll detect the previous working version and click the rollback button (screenshot). The only thing to take into account is that when we change or set a new Heroku config variable, that will trigger a Dyno restart too, so we have to keep in mind where to roll back. It will be almost always previous to our current deploy.

![https://user-images.githubusercontent.com/488556/70787849-e48a0200-1d8f-11ea-817a-9d6067e3b701.png](https://user-images.githubusercontent.com/488556/70787849-e48a0200-1d8f-11ea-817a-9d6067e3b701.png)

## Rollback from terminal

We could also do this using the terminal. you can check the configuration from [the GitHub page](https://blog.heroku.com/releases-and-rollbacks)

We can get the releases.

```bash
heroku releases --app myapp
```

Or get more info from a particular release.

```bash
heroku releases:info v145 --app myapp
```

And when we're sure to roll back to that version.

```bash
heroku rollback v145 --app myapp
```

## Rollback DB

Sometimes we're going to have migrations on a release, so we'll need to check which migrations were added and roll back themmanually, since Heroku doesn't know how to do it. First, we see which migrations are applied.

```bash
heroku run python manage.py showmigrations --app myapp
```

Then we see which commits were added on our release with git log and then log only that range. The first commit isthe oldest then .. and then the newest one. We can grep for migrations, so it's easier to see the app having migrations.

```bash
git log --stat <oldest-commit>..<newest-commit> | grep migrations
```

Once we see which migrations were applied for this release, we need to roll back all those migrations per Django app.

For example:

If the last migration in our commits is a 0061 in the app djangoapp, we need to roll back to 0060.

```bash
heroku run python manage.py migrate djangoapp 0060 --app myapp
```

If we have migrations on more than one app, we'll need to do this for all the apps involved in the changes.

## Backups

Backup schedule will usually be done at the beginning of a project, but it's good to have the documentation.

We can [create a manual backup](https://devcenter.heroku.com/articles/heroku-postgres-backups#creating-a-backup):

```bash
heroku pg:backups:capture --app myapp
```

What we have in all the projects by default is [schedule a new backup](https://devcenter.heroku.com/articles/heroku-postgres-backups#scheduling-backups) every day at 2 am Europe/Madrid timezone.

```bash
heroku pg:backups:schedule DATABASE_URL --at '02:00 Europe/Madrid' --app myapp
```

## Backup Recovery

To make a backup recovery, we need to see which is the backup we want to recover from. We'll get [the backup status](https://devcenter.heroku.com/articles/heroku-postgres-backups#checking-backup-status) of our app.

```bash
heroku pg:backups --app myapp
```

![https://user-images.githubusercontent.com/488556/70788791-f4a2e100-1d91-11ea-98f9-90bfcb074f1a.png](https://user-images.githubusercontent.com/488556/70788791-f4a2e100-1d91-11ea-98f9-90bfcb074f1a.png)

Once we decided on particular backup, we need to run the restore backup for our app. We can follow the [restoring a backup](https://devcenter.heroku.com/articles/heroku-postgres-backups#restoring-backups) guide from Heroku. It would be something like:

```bash
heroku pg:backups:restore a040 DATABASE_URL --app myapp
```

## Check Celery is up and view log

First check dynos is ok: To check if celery is running you can see it in the heroku dashboard:

![1](https://user-images.githubusercontent.com/488556/210351269-570992c7-7257-4b6e-9506-2f897f532c6b.png)

If you want to see the activity charts:

![2](https://user-images.githubusercontent.com/488556/210351279-bb7f436e-9dd1-439f-b7a5-2604da60bc6f.png)

If you want to see the logs:

![3](https://user-images.githubusercontent.com/488556/210351277-c234e701-d3c1-409c-8b9c-ba51a971ddf1.png)

![4](https://user-images.githubusercontent.com/488556/210351275-1103f677-22b3-4092-94fb-1426f32ddd27.png)

In this case, the error is:  HTTP 402 Payment Required

![5](https://user-images.githubusercontent.com/488556/210351273-741e2cb1-ea87-4a51-9201-9350c8e9061a.png)


[Original page](https://javaguirre.notion.site/Disaster-Recovery-Heroku-67ddfb8771d64aa9a3c73776df0cd127)
