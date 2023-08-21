---
title: "Linux-Commands-pt2"
date: 2023-08-21 10:00:00 -700
categories: [Linux]
tags: [linux, cmd, server, snippet, bash]    #TAG names should always be lowercase
---

# Linux Commands

These are some useful Linux commands that I find myself using over and over. This list consists of some fun things I find useful for me, while working.

__In order to not have an endless page of content, see the related pages__

[Linux-Commands-pt1](https://www.linode.com/docs/guides/security/basics/)

## Custom Bash prompt

I got this from one of my favorite content creators, Jay over at [Learn Linux.tv](https://www.learnlinux.tv). I made a slight change to the prompt

Create a new file in your home dir, something like `~/.bash_prompt` and enter the below text

```bash
BRACKET_COLOR="\[\033[38;5;35m\]"
CLOCK_COLOR="\[\033[38;5;35m\]"
JOB_COLOR="\[\033[38;5;33m\]"
USER_COLOR="\[\033[38;5;33m\]"
PATH_COLOR="\[\033[38;5;33m\]"
LINE_BOTTOM="\342\224\200"
LINE_BOTTOM_CORNER="\342\224\224"
LINE_COLOR="\[\033[38;5;248m\]"
LINE_STRAIGHT="\342\224\200"
LINE_UPPER_CORNER="\342\224\214"
END_CHARACTER="|"

tty -s && export PS1="$LINE_COLOR$LINE_UPPER_CORNER$LINE_STRAIGHT$LINE_STRAIGHT$BRACKET_COLOR[$CLOCK_COLO>
```

Save the file. Then open up your bashrc file `~/.bashrc` and put the following at the end of it

```bash
source ~/.bash_prompt
```

Than, just reload your shell

```bash
. ~/.bashrc
```

Alternatively, I've also used the ZSH shell and installed [oh-my-zsh](https://ohmyz.sh/) to make it more productive.

-eof-