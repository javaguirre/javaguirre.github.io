---
title: 'Ansible and the Galaxy, roles'
date: 2015-05-31 18:13:58 +0000
tags: ansible deploy
layout: post
subclass: 'post tag-test tag-content'
---
[According to their site][ansible_web],

> Ansible is a radically simple IT automation engine that automates cloud provisioning, configuration management, application deployment, intra-service orchestration, and many other IT needs.

If you want to know how to start with Ansible here's a [good tutorial][ansible_tutorial], you could also start in their [official documentation][ansible_official]. If you don't know anything about Ansible, please follow the previous links, this post is a bit more advanced.

Two weekends ago [I read the preview of the new Ansible book from O'Reilly][ansible_book]. It was a good introduction of concepts, but still needs a lot of content to be finished, for example It doesn't say anything about [roles][roles] yet, the **cornerstone** of Ansible.

I gotta say this was a great experience for me, and just what I needed to simplify a lot my work as a sysadmin. Coming from the [Puppet][puppet] world, Ansible configuration was very *simple* to understand (just *YAML*, *ini files* and *jinja templates*), and the learning curve much easier than the Puppet one. The layer Ansible adds is very thin and debugging simpler because of It.

The most powerful feature in Ansible are [roles][roles]. Roles are pieces of functionality you can share with others. They can be very flexible, configurable and reusable.

This feature becomes brilliant when you combine it with the [Ansible Galaxy][ansible_galaxy], a repository of roles you can just install with a line in the terminal. You can also install roles from Git repositories.

<pre><code class="language-bash">&gt; ansible-galaxy install bennojoy.mysql

# downloading role 'mysql', owned by bennojoy
...
</code></pre>

So I installed [mysql role by bennojoy][mysql_ansible], Let's see how to use It.

In my case I am using this `Vagrantfile` to test the configuration easily.

<pre><code class="language-ruby">Vagrant.configure(2) do |config|
  config.vm.box = "ubuntu/trusty64"              
  
  config.vm.provision "ansible" do |ansible|     
    ansible.playbook = "site.yml"       
  end                                              
end
</code></pre>

The `site.yml` will be our main Ansible configuration file.

<pre><code class="language-ruby">--- 
- name: Example
  hosts: all
  remote_user: vagrant
  sudo: true
  
  vars_files:                                  
    - mysql.yml
    
  roles:                                     
    - bennojoy.mysql                        
</code></pre>

Briefly, `hosts` defines which hosts will this `playbook`([Ansible's configuration, deployment and orchestration language][ansible_playbook]) be run, the user will be `vagrant` and Ansible should use *sudo* to run It. We added our installed `bennojoy.mysql` role in the roles section, and we will override the necessary variables defined in this role in the `mysql.yml` file.

<pre><code class="language-ruby">---
# mysql.yml
mysql_db: [{ name: acme_db }]
mysql_users: [{ name: acme, pass: acme, priv: "acme.*:ALL" }]
</code></pre>

We defined a user `acme` with permissions `ALL` for the db `acme_db`. When we run this `playbook`, Ansible will create the user and the database defined in the hosts we decide. In our example, they will be created in our machine created by Vagrant.

If you want to know more about how variables work you could read about It in the [Ansible doc site][ansible_variable].

Combining roles is the easiest way to install *elastic search*, [Wordpress][wordpress_ansible], [Ghost][ghost_ansible], [Sentry][sentry_ansible] and other services. It's also a very good way of having your service configuration tidy, under version control systems and your machines organized.

Give it a try!

[ansible_book]: http://www.ansible.com/ansible-book
[ansible_playbook]: http://docs.ansible.com/playbooks.html
[roles]: https://docs.ansible.com/playbooks_roles.html
[ansible_galaxy]: https://galaxy.ansible.com/
[ansible_vault]: https://docs.ansible.com/playbooks_vault.html
[puppet]: https://puppetlabs.com/
[ansible_web]: http://www.ansible.com/home
[ansible_tutorial]: https://medium.com/@denot/ansible-101-d6dc9f86df0a
[ansible_official]: http://docs.ansible.com/
[wordpress_ansible]: https://github.com/roots/bedrock-ansible
[ghost_ansible]: https://github.com/javaguirre/javaguirrenet-ansible
[sentry_ansible]: https://github.com/DandyDev/sentry-ansible-vagrant
[mysql_ansible]: https://github.com/bennojoy/mysql
[ansible_variable]: https://docs.ansible.com/playbooks_variables.html#variable-precedence-where-should-i-put-a-variable

