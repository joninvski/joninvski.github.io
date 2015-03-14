---
layout: post
title: Crashlytics + Github + Gmail + Slack crash management workflow
comments: True
type: "blog"
short_summary: "Short explanation on my crash management workflow at walletsaver.com"

---

In [walletsaver.com](http://walletsaver.com) we have really good crash reporting workflow.

Our android app uses crashlytics (now [fabric.io](http://fabric.io)) for crash reporting.
Although I personally don't like the crashlytics android apk (mainly because it wants to be installed by the IDE)
the service is pretty good.

Whenever a crash occurs the crashlytics catches it (and lets the app crash), sends a report to the crashlytics servers.

<p><img width="80%" src="/img/blog/crashlytics_issue_detail.png" class="blog-image" alt="alt text" /></p>

From there we set an integration with github where an issue is automatically created.
This forces the android developer (me) to don't ignore the crash.

<p><img width="80%" src="/img/blog/github_issue.png" class="blog-image" alt="alt text" /></p>

To quickly know that the problem occurred (we aren't always refreshing crashlytics or github issues page) we have two other integrations.
The first one is with slack, the communication channel were the whole team is always present.

<p><img width="80%" src="/img/blog/github_slack_integration.png" class="blog-image" alt="alt text" /></p>

The second integration is via email. We get an email for each new issue created in github issues.

<p><img width="80%" src="/img/blog/gmail_github_issue_integration.png" class="blog-image" alt="alt text" /></p>
<p><img width="80%" src="/img/blog/gmail_issue.png" class="blog-image" alt="alt text" /></p>

This is an amazing, easy to set up workflow for your managing your crashes.
Not that crashes happen that often... right??
