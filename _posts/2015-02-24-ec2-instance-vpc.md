---
layout: post
title: Back online
comments: True
type: "blog"
short_summary: "Important lesson: If when creating EC2 instance on custom VPCs you are unable to ssh to them, blame the gateway..."

---

Today I was creating AWS EC2 instances using [docker-machine](https://github.com/docker/machine) but if the instances where in a custom VPC the creation process got stuck waiting for sshd server to be up.

I then tried to create these instances manually but the problem persisted. I could not ssh to the public ip after the instance creation.

Error was simple: The custom VPC did not have a gateway to connect it to the internet. To solve it was only necessary to create the Gateway, associate it with the VPC and create a new routing entry that made all remaining traffic (0.0.0.0) to be forwarded to the gateway.
