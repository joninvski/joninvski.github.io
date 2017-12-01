---
layout: post
title: Crashlytics + Github + Gmail + Slack crash management workflow
comments: True
type: "blog"
short_summary: "Short explanation on my crash management workflow for the android app of walletsaver.com"
keywords: ["android"]

---

In [WalletSaver](http://walletsaver.com) we have a pretty cool crash reporting workflow.

The android app relies on crashlytics (now [fabric.io](http://fabric.io)) to detect and report crashes.
Although I'm not 100% in love with the crashlytics android sdk (mainly because it wants to be installed by the IDE and tries to do some magic)
the service is pretty good.

Whenever a crash occurs the crashlytics catches it (and lets the app crash), sends a report to the crashlytics servers.
Similar crashes are correctly grouped the majority of times which is really useful as you can quickly see the biggest problems.

<p><img width="80%" src="/img/blog/crashlytics_issue_list.png" class="blog-image" alt="alt text" /></p>
<p><img width="80%" src="/img/blog/crashlytics_issue_detail.png" class="blog-image" alt="alt text" /></p>

From crashlytics we have set up an integration with github where an issue is automatically created whenever a crash occurs.
This forces the android developer (**me**) to not ignore the crash.

<p><img width="80%" src="/img/blog/github_issue.png" class="blog-image" alt="github issue" /></p>

To quickly know that the problem occurred (we aren't always refreshing crashlytics or github issues page) we have two other integrations.
The first one is with slack, the communication channel were the whole team is always present.

<p><img width="80%" src="/img/blog/github_slack_integration.png" class="blog-image" alt="github slack integration" /></p>

The second integration is via email. We get an email for each new issue created in github issues.

<p><img width="80%" src="/img/blog/gmail_github_issue_integration.png" class="blog-image" alt="gmail github issue" /></p>
<p><img width="80%" src="/img/blog/gmail_issue.png" class="blog-image" alt="gmail github issue integration" /></p>

This is an amazing, easy to set up workflow for your managing your crashes.
Not that crashes happen that often... right??
