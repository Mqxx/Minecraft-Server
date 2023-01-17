<!-- links -->
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
[link-purpurmc-minecraft]: https://purpurmc.org/ 'Purpur Minecraft Server'
[link-vanilla-minecraft]: https://www.minecraft.net/en-us/download/server 'Vanilla Minecraft Server'
[link-sftp-connect-to-server-1]: https://www.youtube.com/watch?v=vtfpzSdlcGM 'WinSCP Tutorial from Nerd Learn'
[link-sftp-connect-to-server-2]: https://youtu.be/SNYnOYWoeAw?t=205 'FileZilla Tutorial from TechHut'
[link-openjdk]: https://openjdk.org 'OpenJDK'
[link-java-parameters]: https://alvinalexander.com/blog/post/java/java-xmx-xms-memory-heap-size-control 'Java starting parameter'

<!-- badges -->
[badge-info]: https://github.com/Mqxx/GitHub-Markdown/blob/main/blockquotes/badge/info.svg 'Info'
[badge-warning]: https://github.com/Mqxx/GitHub-Markdown/blob/main/blockquotes/badge/warning.svg 'Warning'
[badge-error]: https://github.com/Mqxx/GitHub-Markdown/blob/main/blockquotes/badge/error.svg 'Error'



# Minecraft Server

## Introduction

Minecraft Server Hosting on Linux ([Ubuntu Server 22.04.1 LTS][link-ubuntu-server-lts]) - How its done.

In this guide I explain how to properly configure your Minecraft server on Linux, automatically start it on boot, stop it on shutdown and manage it properly.

<!-- info: minecraft version -->
> ![badge-info][badge-info]<br>
> This guide covers the latest version of Minecraft. In our example, this would be **Minecraft Java 1.19.3**, but you can also use the guide for newer versions.
> If anything big changes I will update the guide to the latest version.

We will look how to properly install the [Java Runtime Environment (JRE)][link-openjdk] that we need for our Minecraft server.

We will add a new own minecraft user.

That our server ends up running in the background all the time we use a tool called [tmux][link-tmux].<br>
We will go in details how to work properly with [tmux][link-tmux] and how we can use it properly for our purposes.

To start/stop our server properly we will use [systemd services][link-systemd]. This way we have full control over when our server get's started/stopped.

### Important

⚠ **Please read the individual sections before executing them so that you understand what the commands do!** ⚠

<br>

With that said, let's get started 😉

<br>





---

## Links

- Minecraft server .jar: [PurpurMC][link-purpurmc-minecraft], [Vanilla Minecraft][link-vanilla-minecraft]
- Server software: [Ubuntu Server 22.04.1 LTS][link-ubuntu-server-lts]
- Terminal apps/programms: [Windows Terminal][link-windows-terminal], [KDE Konsole][link-kde-konsole]
- SFTP apps/programms: [WinSCP][link-win-scp], [FileZilla][link-filezilla]
- Tmux: [tmux][link-tmux]
- Systemd: [systemd services][link-systemd]
- OpenJDK (Java): [OpenJDK][link-openjdk]

<br>





---

## Requirements

> Programms/Apps/Knowledge that you need

### Apps

I recommend the installation of the following programs/apps:

- [**ROOT**][link-root-user] Console access to your server (direct access or SSH access using a terminal programm like [Windows Terminal][link-windows-terminal]/[KDE Konsole][link-kde-konsole])
- [**SFTP**][link-sftp] access to your server (SFTP client like [WinSCP][link-win-scp]/[FileZilla][link-filezilla])

### Knowledge

I would suggest that you have basic knowledge in using the terminal 💻

<br>

<!-- warning: sudo -->
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

### Check current version

First we will install the [Java Runtime Environment][link-openjdk].<br>
For this I would recommend that you first check if you already have the correct version of JRE installed.

You can do that with the following command.

```sh
java --version
```

<!-- info: check if jre is already installed -->
> ![badge-info][badge-info]<br>
> If you get the following output you are good to go and do not need to install JRE. If not follow the next steps.
> ```
> openjdk 19.0.1 2022-10-18
> OpenJDK Runtime Environment (build 19.0.1+10-Ubuntu-1ubuntu122.04)                     <-- This is the important line!
> OpenJDK 64-Bit Server VM (build 19.0.1+10-Ubuntu-1ubuntu122.04, mixed mode, sharing)
> ```

<br>

### Serach for newest version

To search for the latest version of JRE use the following command. We will use the [regular expression][link-regex] `[0-9]+` to search for any version.

```sh
apt-cache search --names-only 'openjdk-[0-9]+-jre-headless'
```

<!-- info: search for jre -->
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

### Install lates version

Use the following command to install the latest version of JRE. As an example `19` from our search from above.

> `<version>` is a placeholder for the latest version. 

```sh
apt install openjdk-<version>-jre-headless -y
```

<!-- error: first install -->
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

> Properly set up minecraft user/directory under `/home` on Linux.

### Add the minecraft user

The next thing we do is use the [`useradd`][link-useradd-command] command to create a new user with a new home directory. We will call this user minecraft and use the user's `/home` directory to set up our server.

> `-m` is used to create a directory under `/home` for the minecraft user<br>
> `minecraft` is the name of the user

```sh
useradd -m minecraft
```

<br>

### How to remove user

If you accidentally add a wrong user you can remove it again with the following command

> `-r` removes the directory under `/home`<br>
> `<enter the wrong name here>` is a placeholder for the wrong name. 

```sh
userdel -r <enter the wrong name here>
```

<!-- warning: mail spool warning message -->
> ![badge-warning][badge-warning]<br>
> If you get the following message you can ignore it.
> ```
> userdel: <the wrong name> mail spool (/var/mail/<the wrong name>) not found
> ```

<br>

Nice you have created the minecraft user. 👍

<br>





---

## Prepare Minecraft server .jar

> Download and prepare the server .jar

### Download Purpur MC

Next, we download the latest minecraft server .jar file. Which server .jar you use is up to you. In this guide I will use [purpurmc][link-purpurmc-minecraft]. However, you can also use a pure [vanilla][link-vanilla-minecraft] server. The steps are the same.

<!-- info: how to use SFTP programms -->
> ![badge-info][badge-info]<br>
> How to connect to your server via SFTP you can watch [this tutorial from Nerd Learn (WinSCP)][link-sftp-connect-to-server-1] or [this tutorial from TechHut (FileZilla)][link-sftp-connect-to-server-2]. There are many other tutorials on how to transfer files via SFTP.

Once you have downloaded the server .jar I would advise you to name it uniquely. For example, my server .jar that I downloaded is called `purpur-1.19.3-1894.jar`. The number behind the version usually describes the current build. So I would name the server .jar only `purpur-1.19.3.jar`. Make sure you don't use any spaces or special characters in the filename.

<br>

### Put server .jar in directory

After that use a SFTP program of your choice to put the server .jar in the directory `/home/minecraft`.
  
<!-- info: multiple server -->
> ![badge-info][badge-info]<br>
> If you want to run multiple servers at the same time on your Linux server I would recommend you to create a subfolder in the minecraft direcory where you put your server. For example `/home/minecraft/survival` and `/home/minecraft/creative`. But make sure that you specify the correct paths in all later steps.

<br>

### Check for server .jar location

After you have placed the server .jar in the directory `/home/minecraft` we check again that the server .jar is really in the right place.

For this you can use the following command in the terminal.

```sh
ls /home/minecraft/
```

<!-- info: check location of server .jar file -->
> ![badge-info][badge-info]<br>
> If you get the following output, then the server .jar is in the right place.
> ```
> purpur-1.19.3.jar
> ```

<br>

### Navigate to correct directory

Next we will start the Minecraft server for the very first time to generate the `eula.txt` file. Use the following commands for this. First navigate into the `minecraft` directory.

```sh
cd /home/minecraft/
```

<br>

### Start the first time

After that start the Minecraft server with the following command. Make sure that you are still in the right directory!
  
```sh
java -Xms5G -Xmx10G -jar /home/minecraft/purpur-1.19.3.jar -nogui
```

<!-- info: first server start -->
> ![badge-info][badge-info]<br>
> If you get the following message, then the `eula.txt` was created successfully.
> ```
> Downloading mojang_1.19.3.jar
> Applying patches
> Starting org.bukkit.craftbukkit.Main
> System Info: Java 19 (OpenJDK 64-Bit Server VM 19.0.1+10-Ubuntu-1ubuntu122.04) Host: Linux 5.15.0-58-generic (amd64)
> Loading libraries, please wait...
> [19:44:08 INFO]: Building unoptimized datafixer
> [19:44:10 ERROR]: Failed to load properties from file: server.properties                                               <-- You can ignore these lines
> [19:44:10 WARN]: Failed to load eula.txt
> [19:44:10 INFO]: You need to agree to the EULA in order to run the server. Go to eula.txt for more info.               <-- This is the important line
> ```

<br>

### Accept eula

Now you can open the `eula.txt`. In the last line you should see `eula=false`. You need to set the value to `eula=true`. This way you accept the eula!

```
#By changing the setting below to TRUE you are indicating your agreement to our EULA (https://aka.ms/MinecraftEULA).
#<Date>
eula=false                                                                                                             <-- You need to set this line to true
```

<br>

Now that we have accepted the `eula.txt` we can move on. 🏃‍♂️

<br>





---

## Create start .sh file

> How to create the `start.sh` file

### Create file

Next, we need a start script for our server .jar.

For this we want to create a new text file in the `/home/minecraft` directory with my SFTP program. Optionally you can also do the whole thing via the terminal. I name this file `start.sh`.

Once you have created the `start.sh` file we need to add the following to the file. Open the file and insert the following commands.

### Edit file

```sh
#!/bin/bash

workingDirectory=/home/minecraft

cd $workingDirectory

# run purpur.jar
java -Xms5G -Xmx10G -jar $workingDirectory/purpur-1.19.3.jar -nogui
```

<!-- info: check location of server .jar file -->
> ![badge-info][badge-info]<br>
> Depending on your Linux server you have to adjust the parameters [`-Xms`][link-java-parameters] and [`-Xmx`][link-java-parameters].

Save the file.

<br>

### Change permissions
  
After we have created and saved the file we will make it executable. There are several ways to do this. Either with the following command via the terminal.
  
```sh
chmod +rwx start.sh
```

Or you can use your SFPT program. <kbd>Right Click</kbd> on the `start.sh` file and navigate to **Properties**. In the **Properties** window you will find a table that looks something like this. Set the check marks according to the table.

| ID    | Read                    | Write                   | Execute                 |
|-------|:-----------------------:|:-----------------------:|:-----------------------:|
| Owner | <ul><li>[x] R</li></ul> | <ul><li>[x] W</li></ul> | <ul><li>[x] X</li></ul> |
| Group | <ul><li>[x] R</li></ul> | <ul><li>[ ] W</li></ul> | <ul><li>[ ] X</li></ul> |
| Other | <ul><li>[x] R</li></ul> | <ul><li>[ ] W</li></ul> | <ul><li>[ ] X</li></ul> |

<br>

Now that we have made our file executable we can close it and we are done with our `start.sh` script. 👍

<br>





---
  
## Create console .sh file

> How to create the `console.sh` file

### Create file

We are almost done. Next we just need to create a `console.sh` script so that we can connect to the console at any time after the server has started.

<!-- info: script is optional -->
> ![badge-info][badge-info]<br>
> These steps are optional. But for later usage it is just easier to execute the script that we are creating instead of writing the command into the console each time. Also, this way you don't have to remember the command and can just run the script to connect to the console.

To create the `console.sh` script we first have to create a text file under the `/home/minecraft` directory again. I use my SFTP program again for that. We will name the file `console.sh`. But you can call it whatever you want.

<br>

### Edit file

Once you have created the `consol.sh` file we need to add the following to the file. Open the file and insert the following command. This is the point where we include [`tmux`][link-tmux] for the very first time.

> `-S` Socket path (We will use sockets instead of sessions so that different accounts can access the console later.)

```
tmux -S /usr/local/tmux/shared/minecraft/server attach
```

<!-- info: multiple server sockets -->
> ![badge-info][badge-info]<br>
> Again if you want to run multiple minecraft servers at the same time you can specify a different socket path for each server there.<br>
> As an example:<br>
> `/usr/local/tmux/shared/minecraft/server1`<br>
> `/usr/local/tmux/shared/minecraft/server2`<br>
> You can also choose a different path, just make sure that the path is accessible for all users.

Save the file.

<br>

### Change permissions

After we have created and saved the file we will make it executable. Again there are several ways to do this. Use the following command.

```sh
chmod +rwx console.sh
```

Or use your SFTP program. Set the check marks according to the table.

| ID    | Read                    | Write                   | Execute                 |
|-------|:-----------------------:|:-----------------------:|:-----------------------:|
| Owner | <ul><li>[x] R</li></ul> | <ul><li>[x] W</li></ul> | <ul><li>[x] X</li></ul> |
| Group | <ul><li>[x] R</li></ul> | <ul><li>[ ] W</li></ul> | <ul><li>[ ] X</li></ul> |
| Other | <ul><li>[x] R</li></ul> | <ul><li>[ ] W</li></ul> | <ul><li>[ ] X</li></ul> |

<br>

Now that we have made our file executable we can close it and we are done with our `console.sh` script. 💻

<br>





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
