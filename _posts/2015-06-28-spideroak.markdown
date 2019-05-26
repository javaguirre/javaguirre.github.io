---
title: 'Spideroak'
date: 2015-06-28 17:33:14 +0000
tags: spideroak backup
layout: post
subclass: 'post tag-test tag-content'
---
I've been using a new backup solution, [SpiderOak][spideroak]. SpiderOak is a Zero-knowledge solution, that means 100% of the data is private and only readable by you, ensuring confidentiality of your data.

> With SpiderOak ONE, your files are encrypted long before they cross the internet to our servers. We only store your encrypted data in generalized blocks which means only you can unlock your backup, ever.

[It has a GNU/Linux client among others][clients], and they are working on an Android client, they already have an Android app where you can see your files, but not creating backups from It.

> Dropbox is hostile to privacy, unlike 'zero knowledge' SpiderOak. - Edward Snowden

You have unlimited devices to sync with the service, so It's very handy to have different machines all using the same account.

The SpiderOak client can be used via terminal and It's easy to [install in servers][install] too, so It comes very useful to create backups of certain parts of your server.

You can configure your backup solution for the server like this:

<pre><code class="language-bash">SpiderOak --setup=-
</code></pre>

You setup a new machine.

<pre><code class="language-bash">SpiderOak --include-dir /home/javaguirre/backups
</code></pre>

Include directories you want to backup. it can be used several times.

<pre><code class="language-bash">SpiderOak --selection
</code></pre>

Here you can see the directories included in the backup.

When you are ready to start syncing you can use.

<pre><code class="language-bash">SpiderOak --headless
</code></pre>

You could just [add it to the crontab][crontab].

[crontab]: https://askubuntu.com/questions/189854/how-can-i-start-spideroak-automatically-on-a-headless-server
[spideroak]: https://spideroak.com/
[clients]: https://spideroak.com/solutions/spideroak-one
[install]: https://spideroak.com/faq/how-do-i-install-spideroak-on-a-headless-linux-server
