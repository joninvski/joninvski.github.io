---
layout: post
title: Reduce png image size
comments: True
type: "blog"
short_summary: "Quick tutorial on how to reduce size of png images without any loss of quality."

---

[OptiPNG](http://optipng.sourceforge.net/) allows you to reduce the size of a png assets and it does this without doing any quality.

You would normally execute it:

```
optipng -o7 some-image.png
```

But if you want to do the command automatically for all png images in a folder, you can use the [find](http://man7.org/linux/man-pages/man1/find.1.html) command to help you.

```
find -name '*.png' -exec optipng -o7 '{}'
```

This command is more than enough. Parameter `-o7` is already incading the best compression. But you can tweak a little the command and hope that brute force could lower even more the final image. Just add `-zm1-9` (Note: this can be very slow, just leave it running during the night).
Finally add `--strip all` to remove extra metadata.

You are left with the command:

```
find -name '*.png' -exec optipng -o7 -zm1-9 --strip all '{}'
```

Normally i add the `-preserve` argument, so to avoid changing the file attributes if it didn't change. 
So my final, ultimate png image reduction command is:

```
find -name '*.png' -exec optipng -o7 -zm1-9 -preserve --strip all '{}'
```
