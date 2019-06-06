---
title: Code quality journey
date: 2019-06-06 22:21:00 +0000
tags: quality cicd codacy
toc: true
published: true
---

Coding, programming, developing, name your action! Such a funny and exciting amusement,
sometimes going wrong under stress or when lacking experience in a certain technology.

# The Challenge

![](https://media.giphy.com/media/xTkcERvSGMZOpp9RkI/giphy.gif)

We sometimes need to write code we're not proud of, but we also have mechanisms nowadays to quantify that and improve its quality. Like boy scouts, we can go back and clean
the camp, make more tests because we didn't have enough coverage and we could be not catching all the possible bugs.

![](https://media.giphy.com/media/pFwRzOLfuGHok/giphy.gif)

When I go back and read my code from some time ago, I'm ashamed, and that's a good thing, It means we're growing. Otherwise, we wouldn't notice.

I've always been in the improving code quality path, I know projects and startups need to achieve their goals, otherwise, we all could lose our jobs, but that's why so important to have proper processes and a good CI/CD, control code quality, and measure legacy code so it doesn't grow too much.

I've been a testing advocate for years now. I know what we should test and what is more challenging to test. Why should we refactor if our tests are too complex because we have an architecture problem, what parts are more likely to be problematic, and how to tackle these kinds of refactors. There is still always doubt, risk and terror.

![](https://media.giphy.com/media/2O6tu9nmJ8kYo/giphy.gif)

However, when you're in a small but experienced agency like mine, you need to have metrics, you need to rely on excellent services, so you save all the time possible to do what it gives value to the company. We're continually iterating the process to be better (otherwise what's the fun in doing everything always the same way right? :-)) and we're happy with our processes now, but we always need to be open for improvement.

# The Journey

Start with code reviews, we defined the user story and estimated the cost of development (more on this on the following article), a member of the team got to it, implemented it and created a PR on GitHub.

The first step, in this case, assures two checks are passing within the PR:

- **Gitlab CI** would run the tests and tell us if they passed
- **Codacy** tells us if there were significant regressions in terms of code complexity, code duplication, standards, and code coverage.

If the checks pass at least a developer in the team reviews the PR code for clarity, good patterns, functionality. If everything goes well, the code gets merged to the release branch and, when the sprint ends, to master. We can never remove the human interaction since code patterns, architecture and naming are so dependant on the domain and context of the application.

[Codacy](https://codacy.com) brings us more functionality than other contenders, such as [Danger](https://danger.systems/) or [Code Climate](https://codeclimate.com/), but if you don't like it, you might check it out those. We chose Codacy over the others because it gave us better progress feedback, and they have excellent code coverage integration, much easier to set up than Code Climate (for example). It gave us more than Danger since Danger is usually more used as linter but doesn't get anything regarding code coverage.

When we started using Codacy, something great happened, the Pull Requests were more alive since everybody was trying to minimize the technical debt they were adding, so Codacy gamified our Pull Requests too. :-)

![](/../assets/images/codacy2.png)

Codacy integrates with many languages, we usually use Python Pylint, Bandit, Prospector, and ESLint for Node. Codacy would read these libraries config in your repositories and use that configuration if it exists. Otherwise, you can check the configuration items you want from a particular tool right from their project settings view.

![](/../assets/images/codacy1.png)

At that point, our CI gets triggered (because we merge to master) and if the tests pass the code is automatically deployed to our staging environment, usually Heroku, but also AWS/Kubernetes.

When we want to deploy to production, we create a new tag like **vYYYY.MM.DD** and the CI/CD triggers the deploy to production.

![](/../assets/images/gitlab-ci1.png)

# The Destiny

When the code reaches production, we have two services giving us input so we know if something is wrong or an alert is triggered.

- [Sentry](https://sentry.io) has been my favorite for years in terms of controlling errors in production, it's straightforward to use, to tag, to separate between different environments (backend, frontend, mobile) and it works so well!
- [New Relic](https://newrelic.com/) is mighty, and we use it to detect bottlenecks and performance problems when they occur (or before it is too late if you configure your alerts well!). It's so nice to be able to monitor your queries and see how are they behaving to be able to prevent a problem in the future, New Relic is excellent for that.

![](https://media.giphy.com/media/cI5CL3l9kZ62w0Kh3c/giphy.gif)

What are your favourites? What are your recommendations? Let me know!
