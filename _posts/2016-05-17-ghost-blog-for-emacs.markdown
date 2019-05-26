---
title: 'Ghost blog for Emacs'
date: 2016-05-17 13:06:04 +0000
tags: emacs lisp ghost-post
---
Since I decided the path to the best editor for me is finished (for now) I decided to improve my experience with Emacs. I started learning some Elisp and the best way of learning something is always solving a problem or extending functionality.

[The Ghost blog platform](https://ghost.org/) has now a [Rest API](https://api.ghost.org/), read-only for now, but It will be soon available for CRUD. I decided to create a mode for Ghost, It has a fairly amount of challenge when learning from scratch, how buffer edition works, how to retrieve and call an API through HTTP, how to use **ert** for unitesting...

I use **url.el** for the requests, there is a request.el package, but It sounded a bit more complicated. I also use the **json.el** package and the **markdown-mode**.

The progress can be followed in [github](https://github.com/javaguirre/ghost-blog-emacs), and you can check the [CI in Travis](https://travis-ci.org/javaguirre/ghost-blog-emacs).

It will be available in [melpa](https://github.com/melpa/melpa/pull/3900), the package is called `ghost-blog`.

Before using the module you need to set two variables:

```
(setq ghost-blog-url "https://javaguirre.me/api/v0.1")
(setq ghost-blog-bearer-token "my bearer token")
```

There is a list post view available, It can be triggered with `M-x ghost-blog-get-posts RET`.

![](/content/images/2016/05/Screen-Shot-2016-05-17-at-15-07-17.png)

A detail view of the post. It is triggered form the post list, you put the cursor on top of one of the lines and type `RET` and the post detail view of that post will be seen.

![](/content/images/2016/05/Screen-Shot-2016-05-17-at-15-08-40.png)

There is also a post template, It can be triggered with `M-x ghost-blog-new-post RET`.

![](/content/images/2016/05/Screen-Shot-2016-05-12-at-15-40-14.png)

Edit and create post requests is implemented, but It's not possible to test them because that part of the API is not opened yet.

In conclusion, **the module right now can only read post data because the Ghost API is not yet open for writing purposes**, but this implementation helped me understand better buffers and other Emacs features. Once they open the API completely, the package should be prepared to work with It. :-)






