---
title: Common Git problems and easy incremental solutions for all the family
date: 2019-05-22 22:21:00 +0000
tags: git
cover: '/../assets/images/git.png'
classes: wide
toc: true
---

Git is a gentle beast, but beasts are wild, and they require taming, here are some tips
to resolve 99% of git problems.

![](https://media.giphy.com/media/3oEduIpnblLzPwzJPG/giphy.gif)

Working with developers of different experience levels made me realize we should have
a simple suitable method to resolve all possible issues when working on a git project, that's why
I wrote this tutorial in the first place.

At my company, we follow a small variation of [git flow](https://es.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow),
where there are no **develop** branch, but a **release** one
that gets merged every sprint with **master** and gets recreated for the next sprint until we merge again,
deploying automatically to production.

We did this variation because we found out that keeping the **develop** branch neat and clean
when there are different levels of git knowledge in the company is challenging.

We use **git fetch** to update our git database locally, so we have the latest changes. In case of doubt, we always
rebase and create branches from **origin**, never from a local branch because we might mess up.

When developing a feature, we always use **rebase** to update our current feature from **release**.
We squash the **feature** branches into one commit, so it's easy to rollback a feature once it is reviewed and ready to merge.

```bash
# Being on the feature/my-tasty-feature branch

git fetch origin
git rebase origin/release
```

Creating a feature branch:

```bash
# Being on master or release or any other branch different from the one created

git fetch origin
git checkout -b feature/my-tasty-feature origin/release
```

## First problem and solution

We need to update our code with **release** because we don't have the latest changes. In our
feature branch we can do:

```bash
# Being on the feature/my-tasty-feature branch

git fetch origin
git rebase origin/release
git push -f feature/my-tasty-feature
```

`-f` can only be used on an unmerged personal branch. It means *force* and it rewrites
the git history, so we need to be very careful when using it.

## Second problem and solution

We have to rebase from **release**, we have many commits, and we need to solve several conflicts on different painfully
commits, we need to make our branch changes into one!

First, we need to know how many commits do we want to add
to **release** and squash them.

```bash
# Being on the feature/my-tasty-feature branch

git rebase -i HEAD~5 # where five is the number of commits
```

We replace all the first lines **pick** for **squash** except in the first line and save.

![](/../assets/images/git-problems1.png)

![](/../assets/images/git-problems2.png)

We can change the commit name and put something relevant, save the file.

![](/../assets/images/git-problems3.png)

![](/../assets/images/git-problems4.png)

Done!

![](/../assets/images/git-problems5.png)

Now we need to fix the conflict:

```bash
# Being on the feature/my-tasty-feature branch

git rebase origin/release
```

We fix the conflicts on our favorite editor and add the files, then

```bash
# Being on the feature/my-tasty-feature branch

git rebase --continue
git push -f feature/my-tasty-feature
```

## Third problem and solution

In our GitHub pull request we have commits that don't belong to our branch, but are
from **release**, this might happen for two reasons:

- Because we didn't do the *first solution*
- Because we updated the **master** branch and some commit hashes have changed (This should seldom happen).

[Check the link if you want to know more about how `rebase` works](https://es.atlassian.com/git/tutorials/rewriting-history/git-rebase).

In this case, the best approach (or most straightforward) is usually to rebuild our branch.
First, we need to know how many commits do we want to add
to **release** and squash them, so we'll do the *second solution* entirely.

After doing the second solution, we'll do the following.

```bash
# We are on the feature/my-tasty-feature branch

git fetch origin

git checkout -b feature/my-backup-branch origin/feature/my-tasty-branch

# We delete the branch we're going to rebuild locally

# We are on feature/my-backup-branch
git branch -D feature/my-tasty-branch
git checkout -b feature/my-tasty-branch origin/release

git log feature/my-backup-branch

# We are on feature/my-tasty-branch
# We copy the last commit hash (see screenshot below)
git cherry-pick the-hash-we-have-copied
git push -f origin/feature/my-tasty-branch
```

![](/../assets/images/git-problems6.png)

Done!

These are my tips for people starting on my team when they have problems with Git,
what do you think? Would you change anything? Any recommendations?
