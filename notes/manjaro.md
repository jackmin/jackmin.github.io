# Manjaro

## Multiple Montiors:

* KDE suck on multiple monitors, it cannot remember the layout and KScreen2
  is problematic. So:

  1. disable KScreen2 service: `System Setting` -> `Startup and Shutdown` -> `Background Services` -> `KScreen 2`
  2. Using `xrandr` to setup monitors - write scripts:

	```shell
	#!/bin/sh
	status=$(xrandr | grep "HDMI-A-0 connected")
	monitor=$(xset -q|sed -ne 's/^[ ]*Monitor is //p')
	if [ "${monitor}" = "in Standby" ]; then
		exit 0
	fi
	if [ "${status}" ] && [ "${monitor}" = "On" ]; then
    	logger "status - ${status}, monitor - ${monitor}: do connected"
    	xrandr --output HDMI-A-0 --primary --auto --output eDP --off
	else
    	logger "status - ${status}, monitor = ${monitor}: do disconnected"
    	xrandr --output eDP --primary --auto --output HDMI-A-0 --off
	fi
	```

  3.  Writing script to handle hotplug event of HDMI external monitor:
    - we can use `udevadm monitor --environment --udev` in order to get related udev event informantion
    - write udev rule: `/etc/udev/rules.d/95-monitor-hotplug.rules`:

	`KERNEL=="card0", SUBSYSTEM=="drm", ENV{DISPLAY}=":0", ENV{XAUTHORITY}="/home/jackmin/.Xauthority", RUN+="/home/jackmin/usr/bin/hotplug_monitor.sh"`  
	> **NOTE**: the `ENV{DISPLAY}=":0",ENV{XAUTHORITY}=[[...snip...]]`
	  does matter otherwise it will not find correct display

   - write `hotplug_monitor.sh`:

	```shell
	#!/bin/bash

	# sddm.xsetup.done created by xsetup and removed by xstop
	# We need this file because xrandr causes coredump
	# if it runs before sddm done.
	if [ ! -f /var/run/sddm.xsetup.done ]; then
		exit 0
	fi

	. /home/jackmin/usr/bin/setup_monitor.sh 
	```

## Krunner/baloosearch cannot search files:
* baloon should be version: 5.78.0-1
* in terminal: `balooctl disable && balooctl purge && balooctl enable`
