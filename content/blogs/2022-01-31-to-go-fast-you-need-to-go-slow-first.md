---
title: In technology, as in guitar, to go fast you need to go slow first
date: 2022-01-31 09:00:00 +0000
tags: [learning,junior,development]
published: true
---

Hi! As when we start learning an instrument, we cannot think we can start playing Jimmy Page's riff with a couple of hours of practice, we need to interiorize the ideas and first think without coding, evaluate what we want to do, and design the solution. Designing is always less time costly than coding and refactor a different solution because we messed up.

![](https://media.giphy.com/media/1iqgrUaXbV9NraYrht/giphy.gif)

In guitar, you could start trying to do everything fast but eventually, you would understand you had all these bad habits, and you won't learn fast enough because your technique is not great, the same occurs with coding. Many times when I worked with someone with less experience (or when I had less experience) I realized we follow the same pattern when affronting a problem:

- Read the problem
- Start coding on a prototype
- Have a more or less working prototype with a relatively decent (depending on the experience of the developer) quality of code.
- The code gets reviewed and rejected
- We need to refactor that code and try to add tests so it's not rejected again.

Or worse!

- The reviewer **might** get tired of reviewing an issue that was bad from the beginning and approve it
  because of it, or it won't simply have the best quality because it started with very bad quality and the reviewer
  thinks it's now better, but the quality at the beginning was bad so we set a wrong base level.

This is the path to the DARK SIDE, this goes always wrong and a feature merged in this way will haunt us in the future. When we do this with more than a feature, our technical debt increases fast and 1-minute changes in our code will be 30-minute ones.

## Think before coding, what we can do

It's obvious, but this happens all the time, I still see it sometimes and it's something to avoid entirely. When this happens the best solution is usually just to think.

Close the Pull Request, start again and... THINK! Make diagrams, schedule technical meetings to have feedback from senior people, do pair programming with someone with more experience in the subject, start thinking in the tests rather than in the code (TDD/BDD)... There are many things we should do before, especially the testing because if something is difficult to test It can be done better. Testing will give us more assurances regarding bugs but will give us also a lot of hints regarding code smells and code quality.

There are [many](https://www.manning.com/books/bdd-in-action) [books](https://www.oreilly.com/library/view/clean-code-a/9780136083238/) that can teach us something, [Open](https://github.com/vercel/next.js) [Source](https://github.com/django/django) projects on GitHub, we can see how the developers in the big projects build everything. We need to have passion for what we do because that makes us better every day.
