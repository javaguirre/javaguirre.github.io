---
title: 'javaguirre.ghost, my first Ansible role'
date: 2015-06-01 19:21:31 +0000
tags: ghost-post ansible
layout: post
subclass: 'post tag-test tag-content'
---
Yesterday I created [my first Ansible role, a role to manage, configure and install Ghost][ghost_ansible], [the blogging platform][ghost].

**Why?** Because I checked the others and they weren't *atomic*, I wanted a role managing only Ghost, not the database, installing node, or configuring NGINX all in one.

You can install it this way.

<pre><code class="language-bash">&gt; ansible-galaxy install javaguirre.ghost
</code></pre>

I prefer to use roles doing only one thing right, because It's easier to configure, to replace and maintain and If I have a history of success with a very good NGINX role I don't want to gamble with a new, different one.

In case you want to give it a try, you can override this variables for now, If you need others, [you can use Github to create an issue][issue] and I will answer as soon as I can! :-)

<pre><code class="language-ruby">---

# Roles
ghost_enabled: true
ghost_remove: false

# Ghost service
ghost_production: false
ghost_version: '0.6.3'
ghost_zip_url: "https://ghost.org/zip/ghost-latest.zip"
ghost_user: ghost
ghost_install_dir: /home/ghost

# Ghost config.js
# Production
ghost_production_url: 'http://example.com'
ghost_production_host: '127.0.0.1'
ghost_production_port: '2368'
ghost_production_mail:
  transport: 'SMTP'
  options:
    service: 'Mailgun'
    auth:
      user: ''
      pass: ''
ghost_production_database:
  client: 'sqlite3'
  connection:
    filename: "{{ ghost_install_dir }}/content/data/ghost.db"
    debug: false

# Development
ghost_development_url: 'http://example.com'
ghost_development_host: '127.0.0.1'
ghost_development_port: '2368'
ghost_development_mail:
  transport: 'SMTP'
  options:
    service: 'Mailgun'
    auth:
      user: ''
      pass: ''
ghost_development_database:
  client: 'sqlite3'
  connection:
    filename: "{{ ghost_install_dir }}/content/data/ghost-dev.db"
    debug: false
</code></pre>

[Here you have an example using this role][javaguirre_ghost]. The main `site.yml` could look like:

<pre><code class="language-ruby">---
- name: Javaguirre.net
  hosts: all
  remote_user: vagrant

  vars_files:
    - vars/nginx.yml
    - vars/ghost.yml

  roles:
    - { role: nodejs, sudo: true }
    - { role: nginx, sudo: true }
    - ghost
</code></pre>

My NGINX(`vars/nginx.yml`) configuration would look like.

<pre><code class="language-ruby">---

nginx_events_params:
  - worker_connections 512

nginx_http_params:
  - sendfile "on"
  - tcp_nopush "on"
  - tcp_nodelay "on"
  - keepalive_timeout "65"
  - access_log "/var/log/nginx/access.log"
  - error_log "/var/log/nginx/error.log"

nginx_sites:
  ghost:
    - listen 0.0.0.0:80
    - server_name javaguirre.net

    - access_log /var/log/nginx/javaguirre.net.log
    - error_log /var/log/nginx/javaguirre.net.error.log

    - client_max_body_size 2m

    - location / {
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header HOST $http_host;

        proxy_pass http://127.0.0.1:2368;
        proxy_redirect off;
      }
</code></pre>

And my `vars/ghost.yml`.

<pre><code class="language-ruby">---

ghost_user: vagrant
ghost_install_dir: /home/vagrant/ghost

ghost_production: true
ghost_production_url: 'http://javaguirre.net'
ghost_production_host: '127.0.0.1'
</code></pre>

Any questions? Shoot! :-)

[ghost_ansible]: https://github.com/javaguirre/ghost-ansible
[ghost]: https://ghost.org
[issue]: https://github.com/javaguirre/ghost-ansible/issues
[javaguirre_ghost]: https://github.com/javaguirre/javaguirrenet-ansible
