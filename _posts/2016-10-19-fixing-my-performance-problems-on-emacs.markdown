---
title: 'Fixing my performance problems on Emacs'
date: 2016-10-19 11:34:35 +0000
tags: emacs
layout: post
subclass: 'post tag-test tag-content'
---
As you might know, I've had [several performance problems on Emacs with a large project](https://javaguirre.me/2016/08/22/back-to-my-own-editor-war/), that made me explore other editors.

At some point I missed a lot of things from Emacs, I have everything nicely configured there, and decided to spend some time checking out my issues and trying to fix them.

Most of my problems are due to `projectile`, I realised an option wasn't configured the way I thought!

`projectile-indexing-method` was `alien` when I thought It was `native`. According to `projectile`:

> There are two indexing methods - native and alien.

> The native method is implemented in Emacs Lisp (therefore it is
native to Emacs).  It’s advantage is that is portable and will
work everywhere that Emacs does.  It’s disadvantage is that is a
bit slow (especially for large projects).  Generally it’s a good
idea to pair the native indexing method with caching.

> The alien indexing method uses external tools (e.g. git, find,
etc) to speed up the indexing process.  The disadvantage of this
method is that it’s not well supported on Windows systems.

> By default alien indexing is the default on all operating
systems, except Windows.


That meant I had to enable the `projectile` cache manually, as I did.

```
(setq projectile-enable-caching t)
```

Regarding garbage collection, It seemed [someone did a much better research than me](https://bling.github.io/blog/2016/01/18/why-are-you-changing-gc-cons-threshold/), so I got some of his tips to improve my configuration. Now the threshold is changed when opening the minibuffer (for `helm` mostly).

```
-      (setq gc-cons-threshold 100000000)
+      (defun my-minibuffer-setup-hook ()
+       (setq gc-cons-threshold most-positive-fixnum))
+
+      (defun my-minibuffer-exit-hook ()
+       (setq gc-cons-threshold 800000))
+
+      (add-hook 'minibuffer-setup-hook #'my-minibuffer-setup-hook)
+      (add-hook 'minibuffer-exit-hook #'my-minibuffer-exit-hook)
```

Today was my first day profiling Emacs, It was as easy as using the `profiler-start`, `profiler-stop` and `profiler-report` command (which opens a buffer with the report data, TAB to open the details of each line on the report). You can use `profiler-reset` in case you want to check if your new configuration works better, having a new report.

![](/content/images/2016/10/Screen-Shot-2016-10-10-at-19.15.15.png)

In the meantime, I've added some cool features I was missing from other editors, such as [Dumb Jump](https://github.com/jacktasia/dumb-jump), to jump to definitions in other files, or quick check where a method/class is defined.

![](/content/images/2016/10/Screen-Shot-2016-10-10-at-19.17.52.png)

I also improved my ctags options to exclude `vendor` directories and add support for `coffeescript`.

I'm using [Neotree](https://github.com/jaypei/emacs-neotree) with the command `neotree-projectile-action` to show the project directory tree when necessary.

Another nice package I'm using now is [Anler's centered-window-mode](https://github.com/anler/centered-window-mode), to center the view if we have only one window opened.

![](/content/images/2016/10/Screen-Shot-2016-10-19-at-13.29.42.png)

Everything can be seen in my [Emacs configuration on Github](https://github.com/javaguirre/dotfiles/blob/master/emacs/.emacs.d/javaguirre.org).
