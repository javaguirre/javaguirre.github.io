---
title: 'Gitlab CI configuration, the runner'
date: 2016-01-13 12:52:35 +0000
tags: gitlab ci testing
layout: post
subclass: 'post tag-test tag-content'
---
A month ago I started working in a new project, Last time I wrote about how I built [my development environment][post] from the Virtual Appliance received, today I'm gonna tell you about how I set up [Gitlab CI][gitlab] for continuous integration, our repositories are stored in a **Gitlab CE** instance.

I've built a virtual machine for testing, [the same way I built one for development][post].

![](/content/images/2015/12/Screen-Shot-2015-12-17-at-14-15-17.png)

Gitlab CI gives you a lot of flexibility on how to run your tests, [they provide you with a runner][runner], a service executing your tests and sending the result to your Gitlab instance.

You can run more of one runner concurrently and there are different options on how to set the test environment (docker, shell, ssh...).

Since I'm using a virtual machine on Virtualbox to run my tests, I use the `ssh` strategy.

To configure the runner first [we need to clone the repository][runner].

<pre><code class="language-bash">$ git clone https://gitlab.com/gitlab-org/gitlab-ci-multi-runner.git</code></pre>

Once we have It cloned and [installed following Its instructions][runner], we can register a runner. You'll need a token to configure It, you can find this token in the settings of your project, in the Gitlab website.

<pre><code class="language-bash">$ gitlab-ci-multi-runner register
...
Please enter the gitlab-ci coordinator URL (e.g. https://gitlab.com/ci )
https://gitlab.com/ci
Please enter the gitlab-ci token for this runner
xxx
Please enter the gitlab-ci description for this runner
my-runner
INFO[0034] fcf5c619 Registering runner... succeeded
Please enter the executor: shell, docker, docker-ssh, ssh?
ssh

...

INFO[0037] Runner registered successfully.

Feel free to start it, but if it's
running already the config should be automatically reloaded!
</code></pre>

Once you register the runner, It will create a file in your `$HOME`: `~/.gitlab-runner/config.toml`. It has the following format.

<pre><code class="language-bash"># config.toml

concurrent = 1

[[runners]]
  url = "https://gitlab.com/ci"
  token = "tokenrepo"
  tls-skip-verify = false
  tls-ca-file = ""  
  name = "surf"
  executor = "ssh"
  [runners.ssh]
    user = "root"
    host = "localhost"
    port = "2222"
    identity_file = "/Users/javaguirre/.ssh/ci_rsa"
</code></pre>

You could just edit this information if you change something on your runner, once you're happy with the configuration, you just run the runner.

<pre><code class="language-bash">gitlab-ci-multi-runner run
</code></pre>

or

<pre><code class="language-bash">gitlab-ci-multi-runner start
</code></pre>

![](/content/images/2015/12/Screen-Shot-2015-12-17-at-14-13-45.png)

I want to use Docker to run the tests, but It's not a priority now, once I have It running I'll share my impressions with you. :-)

[runner]: https://gitlab.com/gitlab-org/gitlab-ci-multi-runner
[post]: http://javaguirre.net/2015/12/18/from-ova-to-a-ready-to-develop-vagrant-machine-ansible/
[gitlab]: https://gitlab.com/gitlab-org/gitlab-ce
