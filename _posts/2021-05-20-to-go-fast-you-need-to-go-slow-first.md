---
title: In technology, as in guitar, to go fast you need to go slow first
date: 2021-05-20 09:00:00 +0000
tags: TODO
published: true
---

Hi! Captain Obvious here probably for some people. As when we start learning an instrument, we cannot think we can start playing Jimmy Page's riff with a couple hours of practice, we need to interiorize the ideas and first think without coding, evaluate what we want to do and design the solution, designing is always less time costly than coding and refactor a different solution.

GIF JIMMY PAGE

Of course if we're learning it's always nice to rethink how to do everything and rewrite the code written.

This article could have two different titles, to go fast you need to go slow first or think before coding.
Going slow before going fast is true for almost anything, the example I like the most is when learning a musical instrument, such as the guitar.

You could start trying to do everything fast but eventually you would understand you had all this bad habits, and you couldn't learn fast enough because you technique is awful, the same occurs with coding. Many times when I worked with someone with less experience I realise we follow the same pattern when afronting a problem:

- we Read the problem
- we start coding on a prototype
- we have a more or less working prototype with a very bad quality of code.
- The code gets reviewed and rejected
- We need to refactor that code and try to add tests so it's not rejected again.
- The reviewer might be tired of reviewing an issue that was bad from the beginning and approve it
  because of it, or it won't simply have the best quality because it started with a very bad quality and the reviewer
  thinks it's now better, but because the quality at the beginning was bad so we set a wrong base level.

This is the path to the DARK SIDE, this goes always wrong and a feature merged in this way will haunt us in the future. When we do this with more than a feature, our technical debt increases fast and 1-minute changes in our code will be 30 - minute ones.

## Think before coding

It's obvious, but this happens all the time, I still see it sometimes and it's something to avoid entirely. WHen this happens the best solution is usually.

Close the PR, start again and... THINK! Make diagrams, schedule technical meetings to have feedback from more senior people, do pair programming with someone with experience, start thinking in the testws rather than in the code (TDD/BDD)... There are many things we should do before, specially the testing because if something is difficult to test is wrong, more if we just code it. testing will give us more assurances regarding bugs but will give us also a lot of hints regarding code smells and code quality.
