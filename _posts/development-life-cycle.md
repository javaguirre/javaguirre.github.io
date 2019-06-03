---
title: Code life cycle
date: 2019-05-22 22:21:00 +0000
tags: python vscode django
cover: ''
toc: true
published: false
---

# The challenge

Keep you motor running, head on to the highway. Coding, programming, developing, name your action! Such a funny and exciting amusement,
sometimes going terribly wrong once stressed or just when not knowing well the technolog we're using or we lack experience, we'll said 90% of the time.

We sometimes need to write code we're not proud of, but we also have mechanisms nowdays to quantify that and improve its quality. As boy scouts we can go back and clean
the camp, make more tests because we didn't have enough coverage and we could be not catching all the possible bugs.

![](https://media.giphy.com/media/pFwRzOLfuGHok/giphy.gif)

When I go back and read my code from some time ago, I'm ashamed, and that's a good thing, It means we're growing, otherwise we wouldn't notice.

![]()

I've been always in the improving code quality path, I know projects and startups need to achieve their goals, otherwise we all could lose our jobs, but that's why so important
to have good processes to have a good CI/CD process, control code quality and measure legacy code so it doesn't grow too much.

I've been a testing advocate for years now, I think I know what should need to be tested and what is more difficult to test. Why should we refactor if our tests are too complex, because we have an architecture problem, what parts are more likely to be problematic, and how to tackle these kinds of refactors.

But when you're in a small but experienced agency like mine, you need to have metrics, you need to rely in good services so you save all the time possible to do what it really gives value to the company. We're continually iterating the process to be better (otherwise what's the fun in doing everything always the same way right? :-)) and we're happy with our processes now, but we are always openned for improvement.

# The Journey

Start with code reviews, we defined the user story and estimated the cost of development (more on this on a following article), a member of the team got to it, implemented it and created a PR on GitHub.

The first step in this case is assure two checks are passing within the PR:

- Gitlab CI will run the tests and tell us if they passed
- Codacy will tell us if there were big regressions in terms of code complexity, code duplication , standards and code coverage.

If the checks pass a developer in the team will review the PR code for clarity, good patterns, functionality, etc. If everything goes well the code gets merged to the release branch and, when the sprint ends, to master.

Codacy brings us more functionality than other contenders, such as Danger or Code CLimate, but if you don't like it you might check it out those. You chose COdacy over the others because it gave us a better progress feedback and they have very good code coverage integration, much easier to set up than Code Climate for example. It gave us more than Danger since Danger is usually more used as linter but don't get anything regarding code coverage, for example. Code Climate coverage setup is more complicated and the documentation more fragmented IMHO.

Code integration with many languages, we usually use Python Pylint, Bandit, Prospector, and ESLint for Node, Codacy would read these libraries config in your repos and use that config if it exists, otherwise you can check the configuration items you want from certain tool right from their project settings view.

At that point our CI will get triggered (because we merge to master) and if the tests pass the code will be automatically deployed to our staging environment, usually Heroku, but also AWS/Kubernetes.

When we want to deploy to production, we create a new tag like vYYYY.MM.DD and the CI/CD will trigger the deploy to production.

(Gitlab-CI) example.

GitHub Code reviews.
Codacy (alternatives, Danger Systems and Code Climate), Duplication, Code Coverage, Security, Complexity, good integration with your Stack, Python, Node, etc.
CICD

# The Destiny

When the code reached production, we have two services giving us input so we know if something is wrong or an alert is triggered.

Sentry
New Relic
