---
title: 'Gitlab CI configuration, Ansible and Drupal'
date: 2016-01-14 13:11:50 +0000
tags: [ansible,ci,gitlab,drupal]
---
Yesterday I wrote about [how I configured the Gitlab CI runner for my projects][ci], now I'm gonna show you how I configured the testing environment for [Ansible][ans] provision and a custom Drupal module I'm developing.

I have now two repositories with CI configuration, the one for deploy (using Ansible) and another for the custom Drupal module. The tests will be run in a VM with Drupal and Ansible installed, this can be different in your configuration.

You can enable **Gitlab CI** in your Gitlab repository adding a `.gitlab-ci.yml` on the root directory.

### Deploy repository

The deploy repository has an Ansible playbook and a private role specifically designed for the project, with no external role dependencies whatsoever (see my [previous post][post] to have an idea why).

My `.gitlab-ci.yml` file, the file to configure how the CI will run the tests, for this repository looks like:

<pre><code class="language-yaml">example:
  script:
    - echo localhost > inventory
    # Check syntax
    - ansible-playbook --syntax-check -i inventory ansible/test.yml
    # First run
    - ansible-playbook -i inventory ansible/test.yml --connection=local

    # Idempotence
    - >
      ansible-playbook -i inventory ansible/test.yml --connection=local
      | grep -q 'changed=0.*failed=0'
      && (echo 'Idempotence test: pass' && exit 0)
      || (echo 'Idempotence test: fail' && exit 1)
</code></pre>

It sets `localhost` as the host where tests will run, checks the syntax of the playbook and after It runs the playbook twice.

The `ansible/test.yml` file has a playbook loading the role with default variables.

<pre><code class="language-yaml">- hosts: all

  tasks:
    - include: roles/drupal/tasks/main.yml

  vars_files:
    - roles/drupal/defaults/main.yml
    - test_vars.yml
</code></pre>

In the first run we check if there are any errors, in the second we check if our rules are idempotent. **Idempotence** is critical because we need to control  the results of our tasks on the server (if we modified a file, created a directory, etc), this second run shouldn't modify anything because we haven't changed the configuration.

### Drupal repository

The second repository is the one for the custom Drupal module, this configuration is easy if you already have a VM box or Docker container configured to run Drupal (I have It with the configuration [I wrote about before][post]).

To run my drupal module tests I have this `.gitlab-ci.yml` file.

<pre><code class="language-yaml">stages:
  - test

test:
  script:
    - cd /var/www/drupal/scripts
    - php run-tests.sh --verbose my_module
</code></pre>

It uses the `run-tests.sh` script in your Drupal `scripts` directory to run them in the command-line.

[ci]: https://javaguirre.net/2016/01/13/gitlab-ci-configuration-the-runner/
[post]: https://javaguirre.net/2015/12/18/from-ova-to-a-ready-to-develop-vagrant-machine-ansible/
[ans]: http://www.ansible.com/
