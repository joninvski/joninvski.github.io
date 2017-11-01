---
layout: post
title: Start docker container at boot using systemd
comments: True
type: "blog"
short_summary: "You can use systemd to start docker containers whenever a system boots up. This can be extremely useful if you are creating autoscale images for AWS."

---

To register a new service on Systemd create file `/etc/systemd/system/myapp.service` with the following content.

```
Description=MyApp
Requires=docker.service
After=docker.service

[Service]
Restart=on-failure
ExecStart=/usr/bin/docker run --name ping --rm -e HOSTNAME=google.com -e TIMEOUT=5 willfarrell/ping
ExecStop=/usr/bin/docker stop ping

[Install]
WantedBy=multi-user.target
```

Where we indicate that `myapp` requires docker to start. What this container is doing is simply pinging google every 5 seconds.

Now to start the service:

```
sudo systemctl start myapp
```

And to see if it is really working:

```
docker ps
docker logs -f ping
```

If you want you can ask systemd to stop the service via:

```
sudo systemctl stop myapp
```

The final trick, is enable `myapp` to run every time the system is booted:

```
sudo systemctl enable myapp.service
```
