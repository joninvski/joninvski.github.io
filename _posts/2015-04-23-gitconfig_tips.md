---
layout: post
title: Useful gitconfig configurations and aliases
comments: True
type: "blog"
short_summary: "A quick summary of my git aliases. These shortcuts speed up your normal workflow and help you use git better."

---


First of all let's set vim to do usefull things. Whenever we need to merge or when git difftool is called we use our old trusted vim editor in the diff mode.

```
[merge]
    tool = vimdiff
[diff]
    tool = vimdiff
[difftool]
    prompt = false
```

Extremelly important is to setup our name and email, so all our commits have the same user id. In the user group we also set less as our default pager.

```
[user]
    name = Joao Trindade
    email = trindade.joao@gmail.com
    pager = less -FRSX
```

This is one of the most interesting configuration. Have you ever did *git puhs*. Well no problem. Git will detect your error and correct it after a pause of 1 second.

```
[help]
    autocorrect = 1
```

Indicate to use color on git command outputs whenever possible

```
[color]
    ui = auto
```

Finally the git aliases

```
[alias]
    lg = log --all --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit
    plog = log -p
    slog = log --pretty=format:'%C(yellow)%h %C(blue)%ad%C(red)%d %C(reset)%s%C(green) [%cn]' --decorate --date=short --numstat
    l = log --pretty=format:'%C(yellow)%h %C(blue)%ad%C(red)%d %C(reset)%s%C(green) [%cn]' --decorate --date=short
    staged = diff --cached
    st = status -sb
    a = add
    cob = checkout -b
    up = !git fetch origin && git rebase origin/master
    ir = !git rebase -i origin/master
    who = shortlog -s --
    merged = branch --merged
    notmerged = branch --no-merged
    delete-merged-branches = "!f() { git checkout --quiet master && git branch --merged | grep --invert-match '\\*' | xargs -n 1 git branch --delete; git checkout --quiet @{-1}; }; f"
    sign = commit -s
```

Let's go trough each alias at a time:

* **lg** - Shows a branch history of our repo. Example:

```
  61d5349 - (HEAD, origin/master, origin/HEAD, master) Merge pull request #28 ...
  |\
  | * 8546773 - Upgrading android gradle plugin to 0.12.+ version. ...
  |/
  *   c03be67 - Merge pull request #6 from marcoRS/manifest-fix ...
  |\
  | * eabf551 - Specify AndroidManifest.xml location to run in IDE. ...
  * |   e630bd7 - Merge pull request #18 from ChristianBecker/version0110 ...
  |\ \
  | * | 3424ec2 - Fixed Travis integration (10 months ago) <Christian Becker>
```

* **plog** - Shows the commit log history, with the changes for each commit
* **slog** - Shows the commit log history, with only a small summary (like git status) for each commit
* **l** - Shows the commit log history in very short manner, one commit per line.
* **staged** - Shows the diffs of what has been put on staged (things you did git add)
* **st** - Shorter git status. Example:

```
 ## featXbranch...origin/featureXbranch
  M walletsaver/settings.py
  M wscore/recommender.py
```

* **a** - Short for add
* **cob** - Short for git checkout branch
* **up** - Rebases with upstream. Really usefull if working in large projects.
* **ir** - Interactively rebase with upstream
* **who** - Check how many commits were done per each contributor
* **merged** - Indicates which branches have been fully merged with the current one
* **notmerged** - Indicated which branches have not been merged with the current one
* **delete-merged-branches** - Deletes all git branches already merged with the current one
* **c** - As commit but with sign-off enabled
