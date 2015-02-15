---
layout: post
title: "Whitelisting WLAN Cards in BIOS"
description: "If you read my [previous post][0], you'll know that I'm the proud owner of a new-to-me [HP dv6000 laptop][1] rescued from a trashcan.  I performed several upgrades on the machine - CPU, Memory, adding a solid state disk - but the changing of the WLAN card became a science experiment in BIOS modification."
category: articles
tags: [Little-Endian, HP, BIOS, Laptop Rescue]
comments: true
image:
  feature: bios-header.jpg
  creditlink: http://sbelyea.net
---

If you read my [previous post][0], you'll know that I'm the proud owner of a new-to-me [HP dv6000 laptop][1] rescued from a trashcan.  I performed several upgrades on the machine - CPU, Memory, adding a solid state disk - but the changing of the WLAN card became a huge headache that required modifying and reflashing the laptop's BIOS.

# What's a 'BIOS'?

# Why-LAN?  Or, why do manufacturers block WLAN cards in BIOS?
There are at least two reasons why a laptop manufacturer may want to prevent WLAN cards they they didn't sell from working in their machines.

1. __Fines.  They're afraid of getting hit with a fine by the FCC__.  HP's line of reasoning is that a [WLAN card shipped with a laptop is part of a complete, tested system that has been cleared by the FCC][2].  This is why they provide a list of verified/whitelisted cards in their service manuals and documentation.  Each of these combinations is certified for use with their laptop and has been tested to verify FCC compliance.  Putting a different WLAN card - _even if the card itself has been certified by the FCC_ - in your laptop could potentially cause a violation of FCC rules and result in fines to the manufacturer.

2. __Money.  They want to force you to buy a WLAN card through them__.  Laptop manufacturers typically resell WLAN cards made by major manufacturers like Intel and Broadcom.  To make them HP specific, 


Notes:
-	What is BIOS?
-	What card did the system come with?  Performance perspective?
-	Laptop only has two antennas in the display, 1 x 1 arrangement (1 transmit, 1 receive)
	-	Arrangement limits my card choices, unless I wanted to run additional antennas to the display (require opening up the laptop display unit, tacking down new wires); has to be a 1 x 1 arrangement.
	-	
-	The easy part: buying a new wireless card.  Got the Intel Dual Band Wireless-AC3160.  Supports the 802.11AC (beamforming) specification.

-	The hard part: finding out the card wasn't working.  Why?  Because HP decided to whitelist a small number of WLAN cards in their BIOS.  If your card isn't on there, the system won't boot the BIOS and will throw an error: 104 - unauthorized wireless card.
-	Based on what I've read, you'd typically expect there to be some sort of checksum built in to the BIOS files.  If a checksum fails, the system would refuse to flash the BIOS, or worse.  Could fail, requiring (in the case of a Phoenix BIOS) an emergency USB/floppy disk to be created to reflash and restore the BIOS.




<!-- LINK LIST -->
[0]:/articles/laptop-rescue
[1]:http://support.hp.com/us-en/product/HP-Pavilion-dv6000-Entertainment-Notebook-PC-series/3632100/model/3636594/product-info
[2]:https://www.youtube.com/watch?v=3icT1PQkdbU&feature=player_detailpage#t=1675s
