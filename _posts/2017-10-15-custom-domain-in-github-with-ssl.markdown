---
layout: post
title: How to setup SSL for github custom domain 
comments: True
type: "blog"
short_summary: "If you are hosting your website in github and using a custom domain then your SSL certificate will not work. But you can use cloudfare for free and solve this problem."

---

Today I noticed that https wasn't working for this website.

<p><img width="60%" src="/img/blog/blog-https-failing.png" class="blog-image" alt="ssl fail browser" /></p>

This was due to the certificate provided by *github.com* only validating domains for *github.io*. People that use custom domains were left out.

Fortunatelly there is an easy and free way to fix this. We can use [cloudfare](https://www.cloudflare.com) to host a cache of our website and then point our webservers to that site. Cloudfare certificate will validate our custom domain (technically it won't validate, but it good enough for our purpose).

So [signup with cloudfare](https://www.cloudflare.com/a/sign-up), indicate the website that you want to add, check if you have all the DNS entries listed:

<p><img width="90%" src="/img/blog/cloudfare-dns.png" class="blog-image" alt="cloudfare dns" /></p>

And add the presented DNS servers to your DNS provider (choose the option *Custom DNS*).

<p><img width="90%" src="/img/blog/cloudfare-dns-servers.png" class="blog-image" alt="cloudfare dns servers"/></p>

<p><img width="90%" src="/img/blog/namecheap-custom-dns.png" class="blog-image" alt="cloudfare custom dns"/></p>

Now let's allow cloudfare to fetch the webpage from github via SSL even though of the previous mentioned SSL problem.

In cloudfare just open the crypto section:

<p><img width="60%" src="/img/blog/cloudfare-crypto.png" class="blog-image" alt="cloudfare crypto"/></p>

And select the full ssl option:

<p><img width="90%" src="/img/blog/cloudfare-crypto-full-option.png" class="blog-image" alt="cloudfare crypto full option"/></p>

<p><img width="90%" src="/img/blog/cloudfare-crypto-full-option-explanation.png" class="blog-image" alt="cloudfare crypto full option explanation"/></p>

Then in the page rules section:

<p><img width="50%" src="/img/blog/cloudfare-page-rules-section.png" class="blog-image" alt="cloudfare page rule section"/></p>

Create a new page rule, and enable ssh with the wildcard option:

<p><img width="90%" src="/img/blog/cloudfare-create-new-page-rule.png" class="blog-image" alt="cloudfare create new page rule"/></p>

<p><img width="90%" src="/img/blog/cloudfare-create-new-page-rule-detail.png" class="blog-image" alt="cloudfare page rule detail"/></p>

And all should be working. You might need to wait some minutes for DNS changes to propagate and cloudfare to fetch the page content from github.

<p><img width="60%" src="/img/blog/blog-https-working.png" class="blog-image" alt="ssl fail browser" /></p>

Bonus hint, you can force all pages to be served by *https*. In the *crypto* section just enable "*Always use HTTPS*":

<p><img width="90%" src="/img/blog/cloudfare-always-use-https.png" class="blog-image" alt="cloudfare always use https"/></p>
