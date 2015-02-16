---
layout: post
title: "Whitelisting WLAN Cards"
description: "BIOS Whitelisting of WLAN cards makes my blood boil, so it's great that HP (unintentionally) made it easy to hack.  I performed several upgrades on the machine - CPU, Memory, adding a solid state disk - but the changing of the WLAN card became a science experiment in BIOS modification."
category: articles
tags: [Little-endian, HP, BIOS, Laptop Rescue]
comments: true
image:
  feature: bios-header.jpg
  creditlink: http://sbelyea.net
---

If you read my [previous post][0], you'll know that I'm the proud owner of a new-to-me [HP dv6000 laptop][1] rescued from a trash can.  I performed several upgrades on the machine - CPU, Memory, adding a solid state disk - but the changing of the WLAN card became a huge headache that required modifying and re-flashing the laptop's BIOS.

_At a very high level, the Basic Input/Output System (BIOS) provides a way to initialize hardware components in your machine and make them available to higher level operating systems (like Windows, Linux, Mac OS X, etc.).  A deep understanding of BIOS isn't required to understand this article, but you can read more about it_ [here][4].

## Adding a new WLAN card is tricky business
I started this laptop upgrade project with the expectation that the WLAN upgrade would be a simple affair.  I had purchased an Intel Dual Band Wireless-AC3160 card to replace the old Broadcom 802.11g adapter.  Unplug the existing card, plug in the new one and reattach the antennas.  Simple and fast.

Unfortunately, after the initial swap my machine refused to boot past the BIOS.  An ominous message appeared during POST: 

`104 - Unsupported wireless network device detected. System halted. Remove device and restart.`  

This upgrade just got significantly more challenging.

## Whitelisting WLAN Cards
A quick [search][5] on the internet help unravel the mystery of why my WLAN card wouldn't work.  It appears that HP has a policy of whitelisting WLAN cards in the BIOS of their laptops.  This whitelist has the effect of preventing the system from booting past the BIOS when an _unauthorized_ (or in HP's words, _unsupported_) is detected.  If I wanted to get my WLAN card working, I'd need to either _remove_ the whitelist functionality from the BIOS, or add my card to the whitelist.

## Why do manufacturers block WLAN cards in BIOS?
Before I get in to _how_ I got my card working, let's look at _why_ changing the WLAN card is an issue.  There are at least three reasons why a laptop manufacturer may want to prevent WLAN cards working in their machines.

1. __Fines.__  HP's line of reasoning is that a [WLAN card shipped with a laptop is part of a complete, tested system that has been cleared by the FCC][2].  This is why they provide a list of verified/whitelisted cards in their service manuals and documentation.  Each of these combinations is certified for use with their laptop and has been tested to verify FCC compliance.  Putting a different WLAN card - _even if the card itself has been certified by the FCC_ - in your laptop could potentially cause a violation of FCC rules and result in fines to the manufacturer.

2. __Money.__  Laptop manufacturers typically resell WLAN cards made by major manufacturers like Intel and Broadcom that are re-branded as HP products.  These cards are similar, if not exactly the same, to their non-HP branded counterparts.  The [approved upgrade options][3] for the dv6700 are both limited and antiquated.  If you were buying one of these cards several years ago, they carried a hefty mark-up.

3. __Planned obsolescence.__ If you are unable to upgrade a component of your system to keep it modern, you'll be tempted to purchase a new laptop.

Here's the thing: these reasons are stupid.

-	All wireless cards sold in the United States are required to pass FCC tests and become certified.  By definition, any WLAN card I want to put in my laptop has already been certified for use.   Other manufacturers (ex: Dell) have no such restrictions in their BIOS. 

-	New MiniPCI Express cards support the latest wireless standards like 802.11n and 802.11ac.  There's good reason to believe that future 802.11 enhancements will also be implemented in MiniPCI Express cards.

TL,DR: if I can purchase a card that is certified by the FCC and fits the MiniPCI Express connector on my machine, I can't think of a good reason (beyond money and planned obsolescence) that HP should prevent me from using it.

## Back to the upgrade
Thankfully, I'm not the [only][6] [person][7] who has run in to this whitelisting issue.  If I wanted to get this card working, I'd need to:

1.	Get a working copy of the BIOS ROM;
2.	Edit the ROM file to add my WLAN card to the whitelist, and;
3.	Flash my laptop with the new ROM.

### Opening up the ROM
HP still offers BIOS updates for the HP dv6700 on their support site.  I grabbed a copy of SP39862, which is version F.58 of my laptop's BIOS.  Running the executable results in the contents of the .exe being extracted.  Navigating into the resulting folder gives me a listing of several files:

![File listing of the extracted SP36774.exe][8]

_For reasons unknown to me, SP39862.exe actually extracts to a folder titled SP36774.  The likely explanation is that HP uses the same BIOS image (perhaps with modifications to other files in the package) for several variations of the HP dv6000 line._

The specific file I was interested in was __30CCF58.WPH__.  This was the ROM file that I'd need to edit.  HP uses a Phoenix-based BIOS, so I used Phoenix's BIOS Editor utility to open the ROM and was greeted with this screen:

![File listing of the extracted SP36774.exe][9]

There's a lot going on here, but the important work was actually going to take place outside of the BIOS utility.  The Phoenix BIOS Editor stores the currently opened ROM in an uncompressed form in a _temp_ folder.  That was my next stop.

### Hunt for the WLAN Whitelist
Based on the experiences of [this blog][7], I started looking through each of the BIOSCOD0X.ROM files in the temp folder (there were a total of 7 different ROM files).  These files are entirely in hex and, more importantly, written in [little-endian byte order][10].  

Everything I had read indicated that the whitelist was composed of WLAN card hardware IDs.  These typically take the form of a vendor code, device code and subsystem code (with an optional revision code).  It looks something like this:

`VEN_14E4&DEV_4315&SUBSYS_137C103C`

The __vendor__ portion of the code refers (typically) to the manufacturer of the hardware.  __Device__ codes indicate a specific model of the device, while a __subsystem__ code is a vendor-specific identifier.  If my old wireless card worked on the laptop, it stands to reason that it must have been whitelisted in the BIOS.  Using the hardware ID of the replaced Broadcom 802.11g card as a starting point (listed above), I began searching each of the BIOSCOD0X.ROM files for a matching vendor ID.

This was where I hit my first obstacle.  At this point, I didn't realize that the ROM files were using [little-endian byte order][10].  This is important: I was searching for a vendor ID of _14E4_ in the ROMs, but it was actually stored as _E414_.  Once I identified my mistake, I quickly zeroed in on file BIOSCOD04.ROM.

![The whitelist!][12]

_The highlighted section of the file is the whitelist._

You can immediately notice patterns in the code.  HP whitelisted cards by listing them in a vendor-device-subsystem pattern.  I happened across a useful website ([http://www.pcidatabase.com/][11]) that allowed me to identify each of the cards in the file. 

|Vendor ID		|DEV ID		|Chipset		|
|:-----------|:-----------|:---------------|
|14E4		|4315		|Broadcom Wireless B/G BCM4315|
|14E4		|4311		|Broadcom Wireless A/B/G BCM4311|
|168C		|001C		|Atheros AR5007EG B/G|
|8086		|4222		|Intel 3945 A/B/G|
|8086		|4229		|Intel 4945 A/G/N|

_Remember, the values are going to appear backwards due to the little-endian format!  The Broadcom vendor ID of 14E4 would appear as E414 in the image._

There are multiple subsystem codes for each of these models, but these five were the only _authorized_ WLAN cards for this laptop.  Time to change this!

### Modifying the BIOS ROMs
My new card was an Intel Dual Band Wireless-AC3160 with a hardware ID of:

`VEN_8086&DEV_08B3&SUBSYS_00708086`

Translating this to little-endian in the proper structure, it becomes:

`86 80 B3 08 86 80 70 00`

I selected the last Intel card listed in the file and replaced the hex values with the correct ones for my new WLAN card.  Once the changes were made, I returned to the Phoenix BIOS Editor utility and re-built the ROM file.

### Flashing the BIOS

With my new BIOS ROM successfully created, it was time to flash!  Now, a failed BIOS flash can have some fairly horrendous consequences:

- 	Errors during the boot-up sequence;
-	System instability, or;
-	__Completely bricking your system.__

Like I said, pretty bad.

With fingers crossed, I used HP's WinFlash utility to update the BIOS.  After a nerve-wracking two minutes, the system rebooted and...

...booted into Windows without any issues!  Great - I hadn't bricked my machine and had successfully passed the BIOS WLAN whitelist check.  Now I needed to see if my card was recognized.  A quick check of the device manager confirmed it - I had successfully upgraded my WLAN card!

![My WLAN card appearing in Windows][13]

## A Working, Fairly Modern Laptop
So there you have it.  After several hours of work, a good amount of head-scratching and several bucks (ok, a  bit more than _several_!), I have a fairly modern laptop that can tackle most workloads short of gaming.  I'm always excited to keep electronics from going to the trash heap and consider this little project a tremendous success.

<!-- LINK LIST -->
[0]:/articles/laptop-rescue
[1]:http://support.hp.com/us-en/product/HP-Pavilion-dv6000-Entertainment-Notebook-PC-series/3632100/model/3636594/product-info
[2]:https://www.youtube.com/watch?v=3icT1PQkdbU&feature=player_detailpage#t=1675s
[3]:http://h10032.www1.hp.com/ctg/Manual/c01295877.pdf
[4]:https://en.wikipedia.org/wiki/BIOS
[5]:https://duckduckgo.com/?q=104+-+unsupported+wireless+network+device+detected
[6]:http://www.rechner.org/b1800_bios.html
[7]:http://www.richud.com/HP-Pavilion-104-Bios-Fix/compaq-nc4200/
[8]:/images/whitelist-wlan-cards - 004.png
[9]:/images/whitelist-wlan-cards - 005.png
[10]:https://en.wikipedia.org/wiki/Endianness
[11]:http://www.pcidatabase.com/
[12]:/images/whitelist-wlan-cards - 006.png
[13]:/images/whitelist-wlan-cards - 007.png