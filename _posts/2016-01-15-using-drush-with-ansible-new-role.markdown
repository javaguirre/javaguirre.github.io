---
title: 'Using Drush (Drupal) with Ansible, new role'
date: 2016-01-15 13:57:36 +0000
tags: ansible drupal drush
layout: post
subclass: 'post tag-test tag-content'
---
[Drush][drush] is a good solution to automatise installation and configuration in Drupal from the command line, if we combine It with [Ansible][ans] It makes Drupal *environment* replication a charm.

I needed a role to manage Drupal modules and themes, and I haven't found something like It, so I made a new role, [javaguirre.drupal_deps](https://galaxy.ansible.com/detail#/role/7044).

There are some roles regarding installation of Drupal and Drush ([geerlingguy's](https://github.com/geerlingguy/ansible-role-drush)), others providing everything in the same role ([vlad][vlad]).

I prefer to have smaller roles usually controlling a package application or service than having an all-in-one role. In my opinion roles should manage only one service/application so It's easy to exchange or combine with others, easier to maintain too.

That's how I made the role, It manages and installs modules and themes through **Drush**, It also sets the default theme, [The role is in Github][role]. :-)

You can install It using `ansible-galaxy`:

<pre><code class="language-bash">ansible-galaxy install javaguirre.drupal_deps
</code></pre>

or you could create the `requirements.yml` file and install It using git if you like:

<pre><code class="language-yaml"># requirements.yml

---

- name: javaguirre.drupal_deps
  src: git+https://github.com/javaguirre/ansible-drupal-dependencies-role.git
</code></pre>

and run `ansible-galaxy` with the `-r` option:

<pre><code class="language-bash">ansible-galaxy install -r requirements.yml
</code></pre>

The role defines for now these variables:

<pre><code class="language-yaml">---

drush_bin_path: "/usr/local/bin/drush"
drupal_path: "/var/www/drupal"
drupal_user: apache
drupal_core_modules: []
drupal_themes: []
drupal_modules: []
drupal_other_modules: []
drupal_default_theme: bartik
</code></pre>

A **playbook example** on how to use It would be like this:

<pre><code class="language-yaml">- hosts: all
  remote_user: vagrant
  become: no

  vars:
    drush_bin_path: "/usr/bin/drush"
    drupal_user: http
    drupal_core_modules:
      - contact

    drupal_themes:
      - bootstrap

    drupal_modules:
      - ctools
      - entity
      - ldap

    drupal_other_modules:
      - simpletest

    drupal_default_theme: bootstrap

  role:
    - javaguirre.drupal_deps
</code></pre>

This playbook will enable the core module `contact` if It's not enabled, It will install the `bootstrap` theme and `ctools`, `entity` and `ldap` modules. It will install the `simpletest` module on `/modules/` (the installation path is different, that's why It's a different module list) and will enable the `bootstrap` theme as the default for the current Drupal installation.

In the following days I will work on fixing the tests in Travis CI and adding some tasks regarding the cache or modules and themes uninstall.

If you have  more suggestions, please, [let me know!][issues]

[vlad]: https://github.com/hashbangcode/vlad
[drush]: http://www.drush.org/en/master/
[issues]: https://github.com/javaguirre/ansible-drupal-dependencies-role/issues
[ans]: http://www.ansible.com/
[role]: https://github.com/javaguirre/ansible-drupal-dependencies-role
