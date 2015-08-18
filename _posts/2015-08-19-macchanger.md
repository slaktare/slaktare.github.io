---
layout: post
title:  "macchanger"
date:   2015-08-19 00:15:00
categories: 
---

The description below follows the "Method 3" section reported in [this tutorial](http://wiki.archlinux.org/index.php/MAC_address_spoofing).

Create and edit (as superuser) the file /etc/systemd/system/macspoof@.service for a new startup service: 

	[Unit]
	Description=macchanger on %I
	Wants=network-pre.target
	Before=network-pre.target
	After=sys-subsystem-net-devices-%i.device
	
	[Service]
	ExecStart=/usr/bin/macchanger -r %I
	Type=oneshot
	
	[Install]
	WantedBy=multi-user.target

Enable the service (as superuser) by appending the interface name (e.g., wlan0): 

	user@machine:~# systemctl enable macspoof@wlan.service

Reboot, or stop and start the prerequisite and requisite services in the proper order.
