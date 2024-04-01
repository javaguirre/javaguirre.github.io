---
title: Refactoring
date: 2021-05-20 09:00:00 +0000
tags: [refactoring]
published: true
---

Refactoring makes sense when it's small and gradual. We do It Went to miss
merlot opportunity, When you have a living
a product that can't lose momentum, but
we always make concessions in terms
of speed and sometimes quality goes
down.

Given that there are two moments for me
where/when we can effectively still give value every day.

First, we can spend a bit more time on the code where we are working to develop a feature, we have the context to work on it, but we will need to add new testing and it's a great moment to
spend a bit more of time and improve the code. (Camp better than you found it)

In my opinion, pull requests need to be always small because it's easier to
review and discuss and has less impact overall when on CI/CD. They also need
to be atomic, so it can of changing a lot of code unrelated to the current
functionality but we did refactor there it's better to separate that into 2 PRs

Sometimes applications become monsters and we need to refactor. In my
opinion refactoring can't ever be the
sprint goal, we need to continue building
while refactoring, in that case.

Trying to separate the refactoring into small chunks by applying TDD/BDD
is the best option to avoid a big percentage of problems that may arise.
Refactor should be made fast and reviewed and merged fast, so it's better to
separate and be able to merge fast and within the same day so we avoid
possible conflicts with the features we're developing at the same time.
