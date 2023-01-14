[link-tmux]: https://www.redhat.com/sysadmin/introduction-tmux-linux 'tmux - Terminal Multiplexer'
[link-ubuntu-server-lts]: https://ubuntu.com/download/server 'Ubuntu Server LTS'
[link-systemd]: https://systemd.io 'Systemd - System and Service Manager'
[link-useradd-command]: https://linuxize.com/post/how-to-create-users-in-linux-using-the-useradd-command 'useradd [OPTIONS] <username>'
[link-windows-terminal]: https://apps.microsoft.com/store/detail/windows-terminal/9N0DX20HK701 'Windows Terminal'
[link-kde-konsole]: https://konsole.kde.org/ 'KDE Konsole'
[link-win-scp]: https://winscp.net/eng/docs/lang:de 'WinSCP Client'
[link-filezilla]: https://filezilla-project.org/ 'FileZilla Client'


# Minecraft Server
## Introduction
Minecraft Server Hosting on Linux ([Ubuntu Server 22.04.1 LTS][link-ubuntu-server-lts]) - How its done.

In this guide I explain how to properly configure your Minecraft server on Linux, automatically start it on boot, stop it on shutdown and manage it properly.

So that our server ends up running in the background all the time we use a tool called [tmux][link-tmux].
We will go in details how to work properly with [tmux][link-tmux] and how we can use it properly for our purposes.

We will look how to properly install the Java Runtime Environment (JRE) that we need for our Minecraft server.

To start/stop our server properly we will use [systemd services][link-systemd]. This way we have full control over when our server is started/stopped.

With that said, let's get started 😉

---

## Requirements
> Programms/Apps/Knowledge that you need

I recommend the installation of the following programs/apps:

- **ROOT** Console access to your server (direct access or SSH access using a terminal programm like [Windows Terminal][link-windows-terminal]/[KDE Konsole][link-kde-konsole])
- **SFTP** access to your server (SFTP client like [WinSCP][link-win-scp]/[FileZilla][link-filezilla])

I would suggest that you have basic knowledge in using the terminal 💻

<br>

> __Note__<br>
> All commands we use in this guide will be executed with root privileges. That means I will **NOT** use `sudo` in front of each command!<br>
> If you don't have root privileges you have to use all commands with `sudo`! Otherwise you will probably get a permission error.

---

## Install JRE
> How to install JRE

First we will install the Java Runtime Environment.

---

## Setup Minecraft User
> Properly set up minecraft user/directory under `/home` on linux.

The first thing we do is use the [`useradd`][link-useradd-command] command to create a new user with a new home directory. We will call this user minecraft and use the user's `/home` directory to set up our server.

```sh
useradd -m minecraft
```
<br>

If you accidentally add a wrong user you can remove it again with the following command

```sh
userdel -r <enter the wrong name here>
```
<br>

> __Warning__<br>
> If you get the following error message you can ignore it.
> ```
> userdel: <enter the wrong name here> mail spool (/var/mail/<enter the wrong name here>) not found
> ```

---

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
