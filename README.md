# Minecraft Server
Minecraft Server Hosting on Linux (Ubuntu Server 22.04.1 LTS)

# Startup
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
