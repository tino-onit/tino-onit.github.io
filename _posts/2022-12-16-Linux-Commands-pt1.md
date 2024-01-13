---
title: "Linux-Commands-pt1"
date: 2022-12-16 10:00:00 -700
categories: [Linux]
tags: [linux, cmd, snippet]    #TAG names should always be lowercase
---

# Linux Commands

These are some useful Linux commands that I find myself using over and over. Expect this list to grow over time.

__In order to not have an endless page of content, see the related pages__

[Linux-Commands-pt2](https://tino-onit.github.io/posts/Linux-Commands-pt2/)

---

## Show top n process for CPU and MEM

```bash
ps -eo comm,pcpu --sort -pcpu | head -5; ps -eo comm,pmem --sort -pmem | head -5
```

### Example

```bash
user01@svr01:~$ ps -eo comm,pcpu --sort -pcpu | head -5; ps -eo comm,pmem --sort -pmem | head -5
COMMAND         %CPU
fwupd            0.8
pihole-FTL       0.1
systemd          0.0
kthreadd         0.0
COMMAND         %MEM
gnome-shell      1.8
Xorg             0.8
fwupd            0.8
systemd-journal  0.6
```

>That's a lot to remember, so instead you can just Alias it. Alias (in shells) is just a nice way to "shortcut" commands.
{: .prompt-tip }

Using the above as an example, lets Alias it into a easier to remember command

```bash
alias top5="ps -eo comm,pcpu --sort -pcpu | head -5; ps -eo comm,pmem --sort -pmem | head -5"
```

>So now, all we need to do is run "top5" and it will execute our chained command

```bash
milo@svr01:~$ top5
COMMAND         %CPU
fwupd            0.5
pihole-FTL       0.1
systemd          0.0
kthreadd         0.0
COMMAND         %MEM
gnome-shell      1.8
Xorg             0.8
fwupd            0.8
systemd-journal  0.6
```

## Check for listening ports

On Linux

```shell
sudo netstat -tunlp
```

Will display something like

```shell
root@net-tools:~# sudo netstat -tunlp
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
tcp        0      0 127.0.0.53:53           0.0.0.0:*               LISTEN      310091/systemd-reso
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      310074/sshd: /usr/s
tcp6       0      0 :::22                   :::*                    LISTEN      310074/sshd: /usr/s
udp        0      0 127.0.0.53:53           0.0.0.0:*                           310091/systemd-reso
```

## Various Linux commands

### cat, less, head, tail, wc, nl

```cat``` Short for concatenate, allows you to combine and display the contents of multiple files. This command on a single file will enable you to display its contents.

```less``` The less command allows you to view the contents of a file one page at a time. Compared to the cat command, this allows you to easily review the contents without being overwhelmed by the large quantity of the log file.

```head``` The head command lets you view the contents at the top of the file. To specify the number of lines to be displayed, use the ```-n``` option together with the count of lines, similar to the command below.

```tail``` In contrast to the head command, the tail command allows you to view the end of the file easily. Like the head command, you can specify the number of lines displayed using the ```-n``` option (as shown in the command below).

```wc``` The wc command stands for word count. It's a command-line tool that counts the number of lines, words, and characters in a text file. By default, it prints the count of lines, words, and characters as shown in your terminal, so we can use the ```-l``` option to display the line count only.

```nl``` The nl command stands for number lines. It renders the contents of the file in a numbered line format

```cut``` The cut command allows you to extract specific sections (columns) of lines from a file or input stream by "cutting" the line into columns based on a delimiter and selecting which columns to display. This can be done using the ```-d``` option (for delimiter) and the ```-f``` for position.

```sort``` Command used to sort the lines of text files or input streams in ascending or descending order.

```uniq``` Command allows you to filter out and display unique lines from a sorted file or input stream. _Note_: The ```uniq``` command requires a sorted list to be effective because it only compares the adjacent lines.

-eof-