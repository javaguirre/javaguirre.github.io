---
title: 'Save and Load requests in rest-client for Atom'
date: 2016-03-16 08:28:46 +0000
tags: [atom,rest-client]
---
Some months ago I started to use the [Atom editor][atom] and got deep into how the packages and themes work. After some months I moved back to Emacs and some of the contributions I started were left unfinished.

Since now I'm trying to finish the features I left behind, this past month I've worked hard on having a first version of the save/load requests feature for the [rest-client package][project]. *A Rest client package for Atom* developed by [@ddavison][david].

I am happy to say we have now a [first working version][project]. :-)

In this version the user can save the current request to a collection of requests, so It could be retrieved after to be used again. The recent requests the user created are saved in a different collection too, both collections of requests are stored in two different files within the package.

Here there is a GIF on how this works.

![](/content/images/2016/03/example.gif)
[A lot of improvements can still be made][issue] in this feature, the code and the UI, but since I moved back to Emacs my contributions to the package will probably slow down.

Header image by [DavidAwad](https://github.com/DavidAwad/).

[atom]: https://atom.io/
[david]: https://github.com/ddavison
[project]: https://github.com/ddavison/rest-client
[issue]: https://github.com/ddavison/rest-client/issues/14
