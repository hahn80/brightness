# Brightness Controller
Ubuntu Brightness Settings

1. Install a package:

```
sudo apt install brightnessctl
```

2. Create the services:

```
sudo nano /etc/systemd/system/brightness-day.timer
```

<code>
	[Unit]
	Description=Schedule screen brightness to 90% at 6 AM

	[Timer]
	OnCalendar=*-*-* 06:00:00
	Persistent=true

	[Install]
	WantedBy=timers.target
</code>

```
sudo nano /etc/systemd/system/brightness-day.service
```

<code>
	[Unit]
	Description=Set screen brightness to 90% for daytime

	[Service]
	Type=oneshot
	ExecStart=/usr/bin/brightnessctl set 90%
	User=root
</code>

```
sudo nano /etc/systemd/system/brightness-night.timer
```

<code>
	[Unit]
	Description=Schedule screen brightness to 50% at 6 PM

	[Timer]
	OnCalendar=*-*-* 18:00:00
	Persistent=true

	[Install]
	WantedBy=timers.target
</code>

```
sudo nano /etc/systemd/system/brightness-night.service
```

<code>
	[Unit]
	Description=Set screen brightness to 10% for nighttime

	[Service]
	Type=oneshot
	ExecStart=/usr/bin/brightnessctl set 50%
	User=root
</code>

```
sudo nano /etc/systemd/system/brightness-startup.service
```

<code>
	[Unit]
	Description=Set screen brightness based on time at startup
	After=multi-user.target

	[Service]
	Type=oneshot
	ExecStart=/usr/local/bin/set-brightness

	[Install]
	WantedBy=multi-user.target
</code>

```
sudo nano /usr/local/bin/set-brightness
```

<code>
	#!/bin/bash

	HOUR=$(date +%H)

	if [ $HOUR -ge 6 ] && [ $HOUR -lt 18 ]; then
	  /usr/bin/brightnessctl set 90%
	else
	  /usr/bin/brightnessctl set 50%
	fi
</code>


3. Reload the daemon:

```
sudo systemctl daemon-reload
```

```
sudo systemctl enable brightness-day.service
sudo systemctl start brightness-day.service
sudo systemctl enable brightness-day.timer
sudo systemctl start brightness-day.timer
```

```
sudo systemctl enable brightness-night.service
sudo systemctl start brightness-night.service
sudo systemctl enable brightness-night.timer
sudo systemctl start brightness-night.timer
```

```
sudo systemctl enable brightness-startup.service
sudo systemctl start brightness-startup.service
```

Check the logs:

```
systemctl list-timers --all
sudo systemctl status brightness-day.timer brightness-day.service
```
