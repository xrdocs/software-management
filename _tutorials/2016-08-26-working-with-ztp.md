---
published: true
date: '2016-08-26 10:00 -0700'
title: Working with ZTP
author: Patrick Warichet
excerpt: A brief introduction to Zero Touch Provisioning
tags:
  - iosxr
  - cisco
---
{% include toc icon="table" title="IOS-XR: Working with ZTP" %}

## Purpose of ZTP
ZTP was design to perform 2 different operations
- Download and apply an initial configuration.
- Download and execute a shell script

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

```
host ncs-5001-rp0 {
   hardware ethernet e4:c7:22:be:10:ba;
   fixed-address 172.30.12.54;
   filename "http://172.30.0.22/configs/ncs5001-1.config";
}
```

A more elaborate example that takes into account option 77 or option 15 for IPv6 (user-class) embedded in the dhcp request sent by the client, ZTP embed the string “exr-config” in the DHCP request as described below. The if statement also take into account the capability to re-image the system using iPXE (see iPXE deep dive document)

```
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

```
host ncs-5001-rp0 {
   option dhcp-client-identifier "FOC1947R144";
   fixed-address 172.30.12.54;
   if exists user-class and option user-class = "iPXE" {
      filename = "http://172.30.0.22/boot.ipxe";
   } elsif exists user-class and option user-class = "exr-config" {
      filename = "http://172.30.0.22/scripts/ncs-5001-rp0_ztp.sh";
   }
}
```
### HTTP Server requirement
The HTTP server should be reachable from the management interface or from a data interface if invoked manually and should be readable.
## ZTP utilities
ZTP includes a set of CLI commands and a set of helpers to use within the user script. The CLI command “ztp initiate” can be used to manually trigger the ZTP process, this is particularly useful to test user script or if some manual operation are required before provisioning the box.
### ztp_helper.sh
ztp_helper.sh is a shell script that can be sourced by the user script that provides simple tools to access some XR functionalities.

**xrcmd: Runs an XR exec command**

```
xrcmd “show running”
```

**xrapply: Applies the block of configuration, specified in a file:**

```
cat >/tmp/config <<EOF
!! XR config example
hostname pluto
EOF
xrapply /tmp/config
```

**xrapply_with_reason:** Same as above, but specify a reason for commit history tracking

```
cat >/tmp/config <<EOF
!! XR config example
hostname saturn
EOF
xrapply_with_reason "this is an important name change" /tmp/config
```

**xrapply_string:** Is a one liner to enable easy apply of configuration. Use \n to indicate new lines continuing the configuration.

```
xrapply_string "hostname mars\n interface TenGigE0/0/0/0\n ipv4 address 172.30.0.144/24\n”
```

**xrapply_string_with_reason:** As above, but supplies a note for commit history:

```
xrapply_string_with_reason ”system renamed again" "hostname venus\n interface TenGigE0/0/0/0\n ipv4 address 172.30.0.144/24\n”
```

## ZTP CLI Commands
**ztp initiate:** Invokes a new ZTP DHCP session, logs will go to the console and /disk0:/ztp/ztp.log

ztp initiate allows the execution of a script even of the system has already been configured. This command is useful for testing ZTP without forcing a reload.

```
RP/0/RP0/CPU0:venus#ztp initiate debug verbose interface TenGigE 0/0/0/0 Invoke ZTP? (this may change your configuration) [confirm] [y/n] :
```

**ztp terminate:** Terminates any ZTP session in progress

```
RP/0/RP0/CPU0:venus#ztp terminate verbose
Mon Oct 10 16:52:38.507 UTC
Terminate ZTP? (this may leave your system in a partially configured state) [confirm] [y/n] :y
ZTP terminated
RP/0/RP0/CPU0:venus#
```

**ztp breakout:** Performs a 4x10 breakout detection on all 40 Gig interfaces, by default if no link is detected on any of the four 10Gig interfaces, the port will remain in 40 Gig mode.
The subcommand nosignal-stay-in-breakout-mode will force the port in breakout out mode even if there is no link signal detected but will place the interfaces in shutdown mode. The subcommand nosignal-stay-in-state-noshut will leave the port in breakout mode but will place the four 10Gig interface in no shutdown mode.

```
RP/0/RP0/CPU0:venus#ztp breakout ?
apply  XR configuration commands to apply(cisco-support)
debug  Run with additional logging to the console(cisco-support)
hostname  XR hostname to set(cisco-support)
nosignal-stay-in-breakout-mode On no signal, prefer interfaces to remain in breakout mode(cisco-support)
nosignal-stay-in-state-noshut  On no signal, prefer interfaces to be noshut(cisco-support)
verbose  Run with logging to the console(cisco-support)                           
```

**ztp clean:** Remove all ZTP files saved on disk

```
RP/0/RP0/CPU0:venus#ztp clean verbose
Mon Oct 10 17:03:43.581 UTC
Remove all ZTP temporary files and logs? [confirm] [y/n] :y
All ZTP files have been removed.
If you now wish ZTP to run again from boot, do 'conf t/commit replace' followed by reload.
```
