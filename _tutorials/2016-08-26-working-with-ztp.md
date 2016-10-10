---
published: false
date: '2016-08-26 10:00 -0700'
title: Working with ZTP
author: Patrick Warichet
excerpt: A brief introduction to Zero Touch Provisioning
tags:
  - iosxr
  - cisco
---
## A New Post
## Purpose of ZTP
ZTP was design to perform 2 different operations
•	Download and apply an initial configuration.
•	Download and execute a shell script

## How ZTP works
The ZTP process is executed or invoked from the control plane LXC Linux shell. Prior to IOS-XR 6.1.1 ZTP was executed within the default network namespace and could not access directly the data interfaces. Since 6.1.1 ZTP is executed inside the global-VRF network namespace with full access to all the data interfaces. This document is based on the IOS-XR 6.1.1 implementation.
ZTP is launched from the Linux service manager process (init daemon) when the system reaches level 999 (last processes to be scheduled for execution), ZTP will scan the configuration for the presence of a username, if there are no username configured,  ZTP will fork a dhcp client for both IPv4 and IPv6 simultaneously.
In IOS-XR release 6.1.1 ZTP can also be invoked from the command line interpreter in this case it will start its execution even if a username or a configuration is present in the system. 
If the dhcp response contains an option 67 or option 59 for IPv6, ZTP will download the file using the URI provided by option 67 (or option 59 for IPv6). If the file is not a text file or the file is larger than 500 MB ZTP will erase the file and terminate its execution otherwise it will analyze the first line of the text file received, if the first line of the file starts with “!! IOS XR”, it will consider it as a configuration file and pass it to the command line interpreter for syntax verification and commit it. If the first line starts with “#!/bin/bash” or “#!/bin/sh” ZTP will assume this is a script and start the execution, as illustrate below. If the text file cannot be interpreted as a shell script of a configuration file, ZTP will erase the file and terminate its execution.
The script can use all the Linux tools available in the Control Plane LXC and perform addition HTTP GET using wget or curl for example to install package and/or download and apply configuration blocks. 

## ZTP requirement
ZTP requires 2 external services: a dhcp server and an http server, as illustrated above, the support for tftp has been dropped for security and reliability reasons.

### DHCP server configuration
Note: The DHCP configuration examples described in this document use the open-source isc-dhcp-server syntax.
The basic configuration described below provides a fixed IP address and a configuration file taking in account only the mac address of the management interface.
{% capture "output" %}
```
host ncs-5001-rp0 {
   hardware ethernet e4:c7:22:be:10:ba;
   fixed-address 172.30.12.54;
   filename "http://172.30.0.22/configs/ncs5001-1.config";
}
```
{% endcapture %}
A more elaborate example that takes into account option 77 or option 15 for IPv6 (user-class) embedded in the dhcp request sent by the client, ZTP embed the string “exr-config” in the DHCP request as described below. The if statement also take into account the capability to re-image the system using iPXE (see iPXE deep dive document)

host ncs-5001-rp0 {
   hardware ethernet e4:c7:22:be:10:ba;
   fixed-address 172.30.12.54;
   if exists user-class and option user-class = "iPXE" {
      filename = "http://172.30.0.22/boot.ipxe";
   } elsif exists user-class and option user-class = "exr-config" {
      filename = "http://172.30.0.22/scripts/ncs-5001-rp0_ztp.sh";
   }
}
```
Since ZTP does not require any intervention on the system, an easier way to provision the system is to use the serial number printed on the box and/or the RP, the configuration is then as follow:
host ncs-5001-rp0 {
   option dhcp-client-identifier "FOC1947R144";
   fixed-address 172.30.12.54;
   if exists user-class and option user-class = "iPXE" {
      filename = "http://172.30.0.22/boot.ipxe";
   } elsif exists user-class and option user-class = "exr-config" {
      filename = "http://172.30.0.22/scripts/ncs-5001-rp0_ztp.sh";
   }
}
