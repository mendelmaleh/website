---
title: "How to run a Python service"
date: 2019-04-17T12:46:54-04:00
tags:
  - python
  - linux
  - systemd
---
*This is how I run my Python bots. This example is valid for CentOS, and it's probably very similar on any other distro. You probably will just need to update the paths.*

First, add a `shebang` with the path to `python` (or `python3`, whatever you use) to the top of the file:
```bash
#!/usr/local/bin/python3 -u
```

Then create a `bot.service` file in `/etc/systemd/system`:
```ini
[Unit]
# change this to whatever you want
Description = Telegram bot

[Service]
Type = simple
# change user accordingly
User = mendel
Wants = network.target
After = network.target syslog.target

Restart = on-failure
RestartSec = 10
startLimitIntervalSec = 60

# path to python file dir
WorkingDirectory = /home/mendel/python/bot
# path to python file
ExecStart = /home/mendel/python/bot/main.py
StandardOutput = syslog
StandardError = syslog

[Install]
WantedBy = multi-user.target
```

Now, to test run `sudo systemctl start bot`, and you can check status (and any output you print) by doing `sudo systemctl -l status bot`. `-l` is not required. You can stop it with `sudo systemctl stop bot`.

**`bot` is merely the name of the `.service` file, you can change that as you wish.**

If everything was good, you can `sudo systemctl enable bot` to make it start automatically on boot. Obviously there is also a `disable` command.
