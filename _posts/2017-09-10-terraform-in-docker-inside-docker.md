---
layout: post
title: Terraform in docker inside another docker
comments: True
type: "blog"
short_summary: "Docker recursion: How to create containers with terraform on a docker inside another docker. Useful to quickly test terraform without having to use AWS or similar."
keywords: ["docker", "AWS"]

---

While trying out new technologies I often try to not install them in my host system. Docker has been perfect for this. 
It allows me to keep my main system clean and while trying the most recent versions of new tools.

I've been reading about [Terraform](https://www.terraform.io/). The thing it clearly interested me is that it focus on declarative configuration files instead of imperative ones.

So to try it out I decided for terraform to create a docker container running ubuntu.

```
provider "docker" {
  host = "tcp://not-my-host-docker:2375/"
}

# Create a container
resource "docker_container" "web" {
  image = "${docker_image.ubuntu.latest}"
  name  = "webapp"
}

resource "docker_image" "ubuntu" {
  name = "ubuntu:latest"
}
```

As you can see that configuration expects a docker running on the "not-my-host-docker" hostname.

To have this just create a docker inside docker (aka dind). Just create a new container ith the docker:dind image add add the privileged flag.

```
docker run --privileged --name my-dind -d docker:dind
```

Then link that terraform container to our dint container using the link flag.

First we can ask to plan the terraform.
```
docker run --link my-dind:not-my-host-docker -v $PWD:/data --workdir=/data -i -t hashicorp/terraform:light plan
```

And then apply the change.
```
docker run --link my-dind:not-my-host-docker -v $PWD:/data --workdir=/data -i -t hashicorp/terraform:light apply
```

If you then ask for our docker inside docker for the containers:
```
docker exec my-dind docker ps --all
```

You will see the creatad webapp container
```
CONTAINER ID    IMAGE           COMMAND        CREATED           NAMES
ae5b6ff8f8f0    ccc7a11d65b1    "/bin/bash"    5 seconds ago     webapp
```
