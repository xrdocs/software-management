---
modified: '2016-07-26 15:51 -0700'
published: true
permalink: /techdocs/iPXE_Deep_Dive
excerpt: iPXE Deep Dive
tags:
  - iosxr
position: hidden
---
## A New Post


Introduction

iPXE is an open source boot firmware (licensed under the GNU GPL with some portions under GPL-compatible licenses). It is fully backward compatible with PXE but include several enhancement. The enhancement that are important for IOS-XR are the following:

    Boot from a web server via HTTP
    control the boot process with scripts
    control the boot process with menus
    DNS support

iPXE is included in the network card of the management interfaces only and support for iPXE boot is included in the system firmware (UEFI) of the NCS1K, NCS5k and NCS5500 series routers. All these systems are equipped with a UEFI 64-bits firmware (aka BIOS).

iPXE can run on both IPv4 and IPv6 protocol but cannot use SLAAC for IPv6.

iPXE enumerates all Ethernet interfaces net0, net1, net2, ...
