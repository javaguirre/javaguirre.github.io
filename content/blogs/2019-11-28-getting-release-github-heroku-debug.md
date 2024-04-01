---
title: Get release tag from GitHub Actions to debug on Heroku
date: 2019-11-28 09:00:00 +0000
tags: [github,actions,cicd,heroku]
published: true
---

In TNP we've moved everything to GitHub Actions and we're very happy about it. :-)

But... this article is not about how happy we are using it but a small tip on how we could simplify every day debugging with some simple improvements.

As a good metric oriented company, We measure and monitor everything we believe relevant, this is a solution to control which release we have in production and being able to monitor its breaking changes in a simple way.

We decided to add the git release tag to our deploy and our app footers, with the following steps.

First, We push to production only when a git tag (a release) is created on GitHub.

```yaml
jobs:
  ...

  deploy_production:
    ...

    if: success() && contains(github.ref, 'tags')
```

We deploy to Heroku where is super easy to set a config variable via API, so we decided to:

- Get the release tag from the current release
- Update a `RELEASE_VERSION` variable on Heroku every time a new deploy is successful.
- Profit!

{% raw %}
```yaml
steps:
 - name: Push to Heroku
   run: git push -f https://heroku:${{ secrets.HEROKU_API_TOKEN }}@git.heroku.com/${{ secrets.HEROKU_APP_PRODUCTION }}.git origin/master:master
 - name: Update RELEASE_VERSION on Heroku production
   if: success()
   run: |
    curl -n -X PATCH https://api.heroku.com/apps/${{ secrets.HEROKU_APP_PRODUCTION }}/config-vars \
    -d '{
      "RELEASE_VERSION”: "${{ github.ref }}",
    }’ \
    -H "Authorization: Bearer ${{ secrets.HEROKU_API_TOKEN }}"
    -H "Content-Type: application/json" \
    -H "Accept: application/vnd.heroku+json; version=3"
```
{% endraw %}

We need to set two variables:

- HEROKU_API_TOKEN: Your personal token, you can get it from your [account settings](https://dashboard.heroku.com/account).
- HEROKU_APP_PRODUCTION: Your production app name on Heroku, we don't hardcode it, so it's easier to share these recipes in other projects.

That's It! You'll have something like this on your Heroku app if all goes well.

![Screenshot 2019-11-29 at 22 10 38](https://user-images.githubusercontent.com/488556/69890303-37c27600-12f5-11ea-9280-6a89fa3b74c4.png)

