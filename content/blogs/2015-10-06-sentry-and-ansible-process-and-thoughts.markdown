---
title: 'Sentry and Ansible, process and thoughts'
date: 2015-10-06 15:00:45 +0000
tags: [django,ansible,sentry,postgresql,mysql]
---
At [Selltag][selltag] we have been migrating some services to have a more organized environment. It has been the perfect moment to configure and *document* the servers deploy using [Ansible][ansible]. [The example code is on Github][gh_repo].

**Ansible** is a great and simple way of managing systems, manual changes to servers are over, you can have all systems configuration documented and under version control easily with It.

When I first started using Ansible I realised of Its power, managing/configuring systems is not an easy task, and every tool helping to save time and money is more than welcome.

At **selltag** we have been using [Sentry][sentry] since the beginning, a great way of controlling errors in any part of your services, with support for many programming languages.

### Sentry deploy

We use different community roles from the [Galaxy][ansible-galaxy](the Ansible community roles repository) to deploy Sentry in `ubuntu/trusty64`.

Here my requirements.yml for *Ansible*:

<pre><code class="language-yaml">---

- src: Ansibles.build-essential

- src: Stouts.foundation

- src: Stouts.python

- src: Stouts.postfix

- src: https://github.com/javaguirre/postgresql.git

- src: https://github.com/javaguirre/Stouts.sentry.git
</code></pre>

At the beginning I tried to deploy using MySQL as the database, but we were blocked in an issue [that doesn't seem to be fixed soon][issue_mysql].

No problem! We'll use postgresql instead, we were happy with It anyway. :-)

Using Postgres, the Ansible role gave me some small problems, I think [It might be a role problem][pr_postgres]. The configuration directory for the cluster wasn't created, and I needed to add a couple of fixes for It to work well with my configuration.

We had another problem related to Sentry, We [needed a certain version of `django-jsonfield` for It to work][issue_postgres], but once [we had that sorted out][fork_sentry_commit], everything went well. :-)

[The code is on Github][gh_repo].

[sentry]: https://getsentry.com/welcome/
[ansible]: http://www.ansible.com/
[selltag]: https://selltag.com
[issue_mysql]: https://github.com/getsentry/sentry/issues/1441
[issue_postgres]: https://github.com/getsentry/sentry/issues/1648
[fork_sentry_commit]: https://github.com/javaguirre/Stouts.sentry/commit/3f8d8ce4c3fea54330d08d4dc2fe51ee47e0223c
[pr_postgres]: https://github.com/ANXS/postgresql/pull/100
[ansible-galaxy]: https://galaxy.ansible.com/
[gh_repo]: https://github.com/javaguirre/sentry-ansible-postgres-example
