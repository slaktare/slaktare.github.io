---
layout: post
title:  "macchanger"
date:   2015-08-19 00:15:00
categories: 
---

The following scripts will randomize your network interfaces' MAC addresses on cold boot and after waking your device from suspend. Read [this](https://hal.archives-ouvertes.fr/file/index/docid/858324/filename/Wi-Fi_Stalking.pdf) to understand why randomizing your MAC addresses might be a good idea.

Prerequisites
-------------

Install the `macchanger` package:

    user@machine:~# sudo apt-get install macchanger


Change MAC on startup (via systemd)
-----------------------------------

The description below follows the "Method 3" section reported in [this tutorial](https://wiki.archlinux.org/index.php/MAC_address_spoofing#Method_3:_systemd_unit).

Create and edit (as superuser) the file /etc/systemd/system/macspoof@.service for a new startup service: 

	[Unit]
	Description=macchanger on %I
	Wants=network-pre.target
	Before=network-pre.target
	After=sys-subsystem-net-devices-%i.device
	
	[Service]
	ExecStart=/usr/bin/macchanger -a %I
	Type=oneshot
	
	[Install]
	WantedBy=multi-user.target

Enable the service (as superuser) by appending the interface name (e.g., `wlan0`): 

	user@machine:~# sudo systemctl enable macspoof@wlan0.service

Reboot, or stop and start the prerequisite and requisite services in the proper order.


Change MAC on ifup (via NetworkManager)
---------------------------------------

The description below follows the "Method 5" section reported in [this tutorial](https://wiki.archlinux.org/index.php/MAC_address_spoofing#Method_5:_NetworkManager).

You can use a script which NetworkManager invokes before bringing the interface up. The path to place the script is `/etc/NetworkManager/dispatcher.d/pre-up.d/macpoof.sh`. The first argument `$1` is the interface name.

    #!/bin/bash
    ip link set dev $1 down
    macchanger -a $1
    ip link set dev $1 up
    
Finally, make the script executable:

    user@machine:~# sudo chmod +x macpoof.sh