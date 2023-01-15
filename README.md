[link-tmux]: https://en.wikipedia.org/wiki/Tmux 'tmux - Terminal Multiplexer'
[link-ubuntu-server-lts]: https://ubuntu.com/download/server 'Ubuntu Server LTS'
[link-systemd]: https://en.wikipedia.org/wiki/Systemd 'Systemd - System and Service Manager'
[link-useradd-command]: https://linuxize.com/post/how-to-create-users-in-linux-using-the-useradd-command 'useradd [OPTIONS] <username>'
[link-windows-terminal]: https://apps.microsoft.com/store/detail/windows-terminal/9N0DX20HK701 'Windows Terminal'
[link-kde-konsole]: https://konsole.kde.org/ 'KDE Konsole'
[link-win-scp]: https://winscp.net/eng/docs/lang:de 'WinSCP Client'
[link-filezilla]: https://filezilla-project.org/ 'FileZilla Client'
[link-root-user]: https://en.wikipedia.org/wiki/Superuser 'root User'
[link-sftp]: https://en.wikipedia.org/wiki/SSH_File_Transfer_Protocol 'SFTP'
[link-sudo-command]: https://en.wikipedia.org/wiki/Sudo
[link-regex]: https://en.wikipedia.org/wiki/Regular_expression 'RegEx'

[badge-info]: https://github.com/Mqxx/GitHub-Markdown/blob/main/blockquotes/badge/info.svg 'Info'
[badge-warning]: https://github.com/Mqxx/GitHub-Markdown/blob/main/blockquotes/badge/warning.svg 'Warning'
[badge-error]: https://github.com/Mqxx/GitHub-Markdown/blob/main/blockquotes/badge/error.svg 'Error'

# Minecraft Server
## Introduction
Minecraft Server Hosting on Linux ([Ubuntu Server 22.04.1 LTS][link-ubuntu-server-lts]) - How its done.

In this guide I explain how to properly configure your Minecraft server on Linux, automatically start it on boot, stop it on shutdown and manage it properly.

> ![badge-info][badge-info]<br>
> This guide covers the latest version of Minecraft. In our example, this would be **Minecraft Java 1.19.3**, but you can also use the guide for newer versions.
> If anything big changes I will update the guide to the latest version.

We will look how to properly install the Java Runtime Environment (JRE) that we need for our Minecraft server.

We will add a new own minecraft user.

That our server ends up running in the background all the time we use a tool called [tmux][link-tmux].<br>
We will go in details how to work properly with [tmux][link-tmux] and how we can use it properly for our purposes.

To start/stop our server properly we will use [systemd services][link-systemd]. This way we have full control over when our server get's started/stopped.

With that said, let's get started 😉

<br>

---

## Requirements
> Programms/Apps/Knowledge that you need

I recommend the installation of the following programs/apps:

- [**ROOT**][link-root-user] Console access to your server (direct access or SSH access using a terminal programm like [Windows Terminal][link-windows-terminal]/[KDE Konsole][link-kde-konsole])
- [**SFTP**][link-sftp] access to your server (SFTP client like [WinSCP][link-win-scp]/[FileZilla][link-filezilla])

I would suggest that you have basic knowledge in using the terminal 💻

<br>

> ![badge-warning][badge-warning]<br>
> All commands we use in this guide will be executed with root privileges. That means I will **NOT** use [`sudo`][link-sudo-command] in front of each command!<br>
> If you don't have root privileges you have to use all commands with [`sudo`][link-sudo-command]! Otherwise you will probably get a permission error.

<br>

---

## Update your system
> Check if your system is up to date

First we will update our system to the latest version

You can do that with the following command.

```sh
apt update -y && apt upgrade -y
```

<br>

Now your system is up to date.

<br>

---

## Install Java Runtime Environment (JRE)
> How to install Java Runtime Environment (JRE)

First we will install the Java Runtime Environment.<br>
For this I would recommend that you first check if you already have the correct version of JRE installed.

You can do that with the following command.

```sh
java --version
```

> ![badge-info][badge-info]<br>
> If you get the following output you are good to go and do not need to install JRE. If not follow the next steps.
> ```
> openjdk 19.0.1 2022-10-18
> OpenJDK Runtime Environment (build 19.0.1+10-Ubuntu-1ubuntu122.04)                     <-- This is the important line!
> OpenJDK 64-Bit Server VM (build 19.0.1+10-Ubuntu-1ubuntu122.04, mixed mode, sharing)
> ```

<br>

To search for the latest version of JRE use the following command. We will use the [regular expression][link-regex] `[0-9]+` to search for any version.

```sh
apt-cache search --names-only 'openjdk-[0-9]+-jre-headless'
```

> ![badge-info][badge-info]<br>
> That will give you back something similar like the following.
> ```
> openjdk-11-jre-headless - OpenJDK-Java-Laufzeitumgebung, verwendet Hotspot JIT (ohne GUI)
> openjdk-17-jre-headless - OpenJDK-Java-Laufzeitumgebung, verwendet Hotspot JIT (ohne GUI)
> openjdk-18-jre-headless - OpenJDK-Java-Laufzeitumgebung, verwendet Hotspot JIT (ohne GUI)
> openjdk-8-jre-headless - OpenJDK-Java-Laufzeitumgebung, verwendet Hotspot JIT (ohne GUI)
> openjdk-19-jre-headless - OpenJDK Java runtime, using Hotspot JIT (headless)                <-- This is the latest version that we want
> ```

<br>

Use the following command to install the latest version of JRE. As an example `19` from our search from above.

> `<version>` is a placeholder for the latest version. 

```sh
apt install openjdk-<version>-jre-headless -y
```

> ![badge-error][badge-error]<br>
> If you get a similar error then just run the install command again.
> ```
> ...                                                       <-- Many error lines
> E: Sub-process /usr/bin/dpkg returned an error code (1)   <-- End of error
> ```

<br>

Confirm with <kbd>Enter</kbd> when asked which services should be restarted.

And you are done installing the JRE. 😀

<br>

---

## Setup Minecraft User
> Properly set up minecraft user/directory under `/home` on linux.

The next thing we do is use the [`useradd`][link-useradd-command] command to create a new user with a new home directory. We will call this user minecraft and use the user's `/home` directory to set up our server.

> `-m` is used to create a directory under `/home` for the minecraft user<br>
> `minecraft` is the name of the user

```sh
useradd -m minecraft
```
<br>

If you accidentally add a wrong user you can remove it again with the following command

> `-r` removes the directory under `/home`<br>
> `<enter the wrong name here>` is a placeholder for the wrong name. 

```sh
userdel -r <enter the wrong name here>
```

> ![badge-warning][badge-warning]<br>
> If you get the following error message you can ignore it.
> ```
> userdel: <the wrong name> mail spool (/var/mail/<the wrong name>) not found
> ```

<br>

Nice you have created the minecraft user. 👍

<br>

---

# 👇 WORK IN PROGRESS 👇

## Startup
> How to configure your server to run at startup.

```
[Unit]
Description=Minecraft (survival.mc@host) server startup service.

[Service]
Type=forking
WorkingDirectory=/home/minecraft/survival.mc@host
ExecStartPre=/usr/bin/tmux -S /usr/local/tmux/shared/minecraft/survival.mc@host new-session -s 'survival.mc@host' -d
ExecStart=/usr/bin/tmux -S /usr/local/tmux/shared/minecraft/survival.mc@host send '/home/minecraft/survival.mc@host/start.sh' Enter
ExecStop=/usr/bin/tmux -S /usr/local/tmux/shared/minecraft/survival.mc@host send 'stop' Enter
ExecStopPost=delay 5
Restart=on-failure

[Install]
WantedBy=multi-user.target
```
