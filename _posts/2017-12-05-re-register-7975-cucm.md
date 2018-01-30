---
layout: post
title: How to re-register Cisco IP Phone 7975 to Cisco Unified Communications Manager (CUCM)
subtitle:
tags: [ip phone, cucm, load]
---

Was tested on Cisco IP Phone 7975

Issue: Ussually, when you have a phone, it's already connected to some Call Manager. And it's not clear how to change it to another Call Manager.

In order to specify your own IP for CM you need to do the next:

1. Go to "Settings" (a button on the phone) -> "Security Configuration" -> "Trust List
2. If you see the locked lock icon, you need to unlock it by pressing \*\*\# and delete the ITL File.
3. Go to "ITL file" and erase it.
4. After some internal processing go to "Settings" -> "Network Configuration" -> "IPv4 Configuration" -> "TFTP Server 1" -> "Edit"
5. Put your CUCM server IP and Save
6. Now it should pull configuration from configured CUCM


TIP: If you want to reboot your phone you can use the next combination:  \*\*\#\*\*  (tested on  7975)
