---
title: 'Rest-client atom package updates'
date: 2016-08-29 11:14:45 +0000
tags: coffee atom rest
layout: post
subclass: 'post tag-test tag-content'
---
I had a bit of time today to close some open issues:

* The oldest one was [Fix Travis CI environment](https://github.com/ddavison/rest-client/pull/48), at some point the way the test environment for Atom works changed and we were outdated, that's now fixed. We sometimes have a problem with a file, but we'll need more time to fix that, it seems a `timeout` problem or an `unlink file` one.

* We catched an exception to [fix wrong json payload](https://github.com/ddavison/rest-client/pull/50). If you add an invalid json payload now you'll get a nice error.

![](/content/images/2016/08/Screen-Shot-2016-08-29-at-13-12-54.png)

* It's possible to [use a proxy server](https://github.com/ddavison/rest-client/pull/49) with the `rest-client`, wasn't possible before.

For the future we'll need to improve the test coverage and the UI for the `Advanced` options (like `strict ssl`, `proxy server`... it's ugly now).
