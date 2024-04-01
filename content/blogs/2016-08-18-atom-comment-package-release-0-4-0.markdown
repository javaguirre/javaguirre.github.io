---
title: 'Atom comment package release 0.4.0'
date: 2016-08-18 10:31:28 +0000
tags: [atom,javascript,comment]
---
Today I've released the [0.4.0](https://atom.io/packages/comment) version of the [comment package for Atom](https://github.com/javaguirre/comment).

Some nice [contributions to add sass, elm and terraform language support](https://github.com/javaguirre/comment/pulls?q=is%3Apr+is%3Aclosed), Thank you!

I've been refactoring the code, adding unittests to improve the current coverage which was nearly none, improved [the indentation of the comments in the code, thanks to @danielbayley for the suggestions!](https://github.com/javaguirre/comment/issues/25)

We also fixed some problems when the language was not covered by the package and the `comment:toggle` shortcut was executed.

If you think [something's missing, please let me know!](https://github.com/javaguirre/comment/issues) :-)
