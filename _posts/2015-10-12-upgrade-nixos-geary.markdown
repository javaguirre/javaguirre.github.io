---
title: 'Upgrade NixOS packages: geary, spideroak, phpcs'
date: 2015-10-12 15:21:32 +0000
tags: nixos spideroak geary phpcs
layout: post
subclass: 'post tag-test tag-content'
---
First an article about [what's NixOS trying to achieve][article] and [NixOS][nixos] if you want to know what is all about.

Today I started getting my hands dirty with **Nixpkgs** packages, the package collection of [NixOS][nixos].

I had used NixOS as my primary OS only for three days now, and I found some packages needed an upgrade because I had recent versions in my other OS.

In my case [geary][geary] needed to be [bumped to version 0.10.0][geary_pr], this was a nice first approach to Nixpkgs for me.

![](/content/images/2015/11/Screenshot-from-2015-10-12-18-16-42.png)

To install the package from the Nixpkgs It's necessary to add the route to the [repository clone][nixpkgs], in my case.

<pre><code class="language-bash">nix-env -i geary -f ~/code/nixos/nixpkgs/default.nix
</code></pre>

This will install geary in my system with the version in that repository, in this case `0.10.0`.

I've been using the new version (0.6.1) of [SpiderOak][spideroak] for a while, I needed It too and [I made an upgrade][spideroak_pr]. I have some problems with directories, but [following the NixOS manual made the debugging easier][manual].

To install SpiderOak I had to enable the *allow unfree variable*.

<pre><code class="language-bash">export NIXPKGS_ALLOW_UNFREE=1
nix-env -i spideroak -f ~/code/nixos/nixpkgs/default.nix
</code></pre>

Finally I wanted to get the phpcs (PHP Code Standard tool) working for my emacs `php-mode`, It wasn't in the package manager, so I read the php packages and some configuration similar to what I wanted to achieve, [et volia!][phpcs_pr].

To try them you could use my [Nixpkgs repository][nixpkgs].

I only scratched the surface of package management in Nix today, but there will be more as soon as I need more from It. :-)

**Reference**: [Create and debug NixOS packages][manual].

[nixos]: http://nixos.org/
[nixpkgs]: https://github.com/javaguirre/nixpkgs
[manual]: https://nixos.org/wiki/Create_and_debug_nix_packages
[spideroak]: https://spideroak.com/
[geary]: https://wiki.gnome.org/Apps/Geary
[article]: https://www.domenkozar.com/2014/03/11/why-puppet-chef-ansible-arent-good-enough-and-we-can-do-better/
[geary_pr]: https://github.com/NixOS/nixpkgs/pull/10325
[spideroak_pr]: https://github.com/NixOS/nixpkgs/pull/10341
[phpcs_pr]: https://github.com/NixOS/nixpkgs/pull/10345
