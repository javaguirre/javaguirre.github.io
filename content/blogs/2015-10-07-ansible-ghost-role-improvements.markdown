---
title: 'Ansible Ghost role, improvements'
date: 2015-10-07 14:39:28 +0000
tags: [ghost-post,ansible]
---
Hi!

Today I improved some parts of the [Ansible ghost role][ghost].

[The tests are now passing][travis], testing also Idempotence in the Ansible role.

[Custom theme installation through git is now available][readme].

I plan to add support for Systemd, although I am using Upstart for now, so only the service for Upstart is available.

You can follow [the development of the role on Github][ghost].


[travis]: https://travis-ci.org/javaguirre/ghost-ansible
[ghost]: https://github.com/javaguirre/ghost-ansible
[readme]: https://github.com/javaguirre/ghost-ansible/blob/master/README.md
