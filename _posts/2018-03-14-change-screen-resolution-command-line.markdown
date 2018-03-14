---
layout: post
title: Change screen resolution to something working from the command line
comments: True
type: "blog"
short_summary: "Sometimes I setup a single external monitor output on my laptop. When I take the laptop home I disconnect the monitor and forget then how to turn back the laptop monitor."
keywords: ["bash"]

---
Sometimes I setup a single external monitor output on my laptop. When I take the laptop home I disconnect the monitor and forget then how to turn back the laptop monitor.

This is how I solve it. First i go to a virtual console outside X by pressing `Control + Alt + F1`. Then enter my crendentials and run:

```bash
### Change the laptop screen to something usefull
sudo bash -c 'chvt 7; xrandr --display :0.0 --auto`
```

Where `chvt` changes back to the X virtual terminal, and `xrandr --display :0.0 --auto` brings back my laptop monitor.
