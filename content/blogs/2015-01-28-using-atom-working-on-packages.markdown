---
title: 'My two weeks using Atom'
date: 2015-01-28 21:20:45 +0000
tags: [vim,atom,rest-client]
---
I've been using the [Atom editor][atom_editor] for a couple of weeks, to give it a try. I found it appealing, but not yet for the every day use. Here is why.

The biggest problem I have are the bugs on [vim-mode][vim_mode] or related to it, you won't be able to use `ci}` motions/modificators and I ended up using `bct}`, but still, you miss those very common commands. Those are bugs regarding `atom-keymap` on GNU/Linux I [helped debug two weeks ago][issue_keymap].

Working on the terminal with `tmux` and `emacs` every day, for me switching for a while to a GUI IDE was a bit of a hassle. In the end I was fast due to vim-mode too, but I have a lot of transitions to the terminal during the day and errors due to bugs on the editor.

I made my own theme, completely inspired on [vim Jellybeans][jellybeans], my favourite dark theme. It still need some work, but I'm getting there I think.

![](/content/images/2015/01/Screenshot-from-2015-01-28-22-21-52.png)

I also worked on some packages, but found difficult to play with it due to the lack of good documentation. I found a nice one, but not yet completed, [rest-client][rest-client], a REST client for Atom, and added the option of using custom headers. I think we could improve it so It's more useful (save common requests, common headers, etc), but this was a nice way of getting into Atom package development and learn how it works underneath a bit.


### Commands/tips I found nice to develop packages/themes

Opening the action panel (`Ctrl-Shift-P`):

* Key Binding Resolver `Ctrl-.`: So you know what key is doing what when pressing it.
* Webkit Dev tools (obviously).
* [Sublime text match patterns][sublime], Atom uses the same syntax to match expressions.
* `atom --dev`.

### I didn't like

Atom editor gives more importance to Atom editor errors than linter errors (left in the image below, Atom editor warnings). I guess is something only until the 1.0 version, so nothing really annoying.

![](/content/images/2015/11/Screenshot-from-2015-01-28-11-51-43.png)

### I liked

The Linter error bar and git gutter have a nice UX in the editor, and you could easily style it in your theme.

Separating environments to write/debug packages for the editor.

![](/content/images/2015/11/Screenshot-from-2015-01-28-19-59-10-1.png)

### Atom Plugins I liked the most

* [Term2][term2] Nice terminal
* [Project Manager][manager]

### *Simple* plugins I missed from Emacs

I missed more packages, but these would be easier to develop, at least a simple first version of it.

* [Rainbow delimiters][rainbow]
* `ex mode`

I know it's still a work in progress, but I am liking how It's being improved, It's going fast and as soon as some side packages are stabilized, the user experience could be much better.

After this two weeks I am back to Emacs, although I never really left. :-)

[issue_keymap]: https://github.com/atom/vim-mode/issues/403
[jellybeans]: https://github.com/nanotech/jellybeans.vim
[rest-client]: https://github.com/ddavison/rest-client
[atom_editor]: https://atom.io/
[vim_mode]: https://github.com/atom/vim-mode
[sublime]: http://sublimetext.info/docs/en/reference/syntaxdefs.html
[rainbow]: http://www.emacswiki.org/emacs/RainbowDelimiters
[term2]: https://github.com/webBoxio/atom-term2
[manager]: https://github.com/danielbrodin/atom-project-manager
