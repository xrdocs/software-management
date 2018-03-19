---
published: false
date: '2018-02-22 17:14 -0800'
title: ZTP integration with NSO
---
# Introduction
Network Services Orchestrator is a Cisco tool that provides end-to-end orchestration that spans multiple domains in your network. Using strict, standardized YANG models for both services and devices and a highly efficient abstraction layer between your network services and the underlying infrastructure, the orchestrator lets you automate Cisco and other vendor's devices.

## NSO

NSO also provides 2 differents way to interacts with IOS-XR:

* The IOS-XR CLI NED for CLI configuration
* The Netconf NED for Netconf/Yang configuration

The IOS-XR CLI NED for your version of NSO should be downloaded and installed as a packages.
For the Netconf NED, there are 2 ways to create the package

1. Download all the models supported by XR on github: [Yang models for Cisco IOS-XR](https://github.com/YangModels/yang/tree/master/vendor/cisco/xr) and use the ncs-make-package command to create the package, once created you can install the package inside NSO.

2. Use the NSO pioneer tool [Your Swiss army knife for NETCONF, YANG and NSO NEDs](https://github.com/NSO-developer/pioneer) to retrieve all the models from a device and create the package.

The advantage of the second method is that you are certain that all the models are effectively supported by the device but the retrieve operation can take some time.

NSO has a set of REST/RESTCONF northbound API that can be used to provision a devices using simple HTTP GET/PUT/POST request. In short only 3 operations are required:

* Creating the device and associate it with the correct NED (Netconf/IOS-XR CLI)
* Exchange the RSA keys between the device and NSO
* synchronize the configuration with NSO

## IOS-XR
The base image of IOS-XR requires the installation of the K9 (Crypto support) package if you decide to use the CLI NED and the MGBL (SNMP/Netconf/telemetry, etc.) package if you device to use to use Netconf NED. A base configuration that enables these features should also be placed onto the device.

## ZTP

ZTP has supports for both shell and python scripts, IOS-XR comes with an rich environment of shell tools and python libraries. In this example we will use a python based ZTP script and will leverage the python-netclient, python-json and the embedded ztp_helper libraries to provision the device in NSO.


## DHCP configuration
For ZTP to operate a valid IPv4/IPv6 address is required and the DHCP server must send a pointer to the configuration script via option 67. Here is an example of configuration

```
#
# DHCP Server Configuration file.
#
allow bootp;
allow booting;
ddns-update-style interim;
option time-offset -8;
ignore client-updates;
default-lease-time 300;
log-facility local7;
authoritative;

subnet 192.168.1.0 netmask 255.255.255.0 {
  option subnet-mask                  255.255.255.0;
  option broadcast-address            192.168.1.255;
  option routers                      192.168.1.1;
  option domain-name-servers          192.168.1.10;
  option domain-name                  "cisco.local";
}
host ncs-5001-1 {
  option dhcp-client-identifier    "FOC2018R1QU";
  fixed-address                    192.168.1.16;
  if exists user-class and option user-class = "iPXE" {
    filename = "http://192.168.2.10/ipxe/ncs5k.ipxe";
  } elsif exists user-class and option user-class = "exr-config" {
    filename = "http://192.168.2.10/scripts/ncs-5001_nso_ztp.py";
  }
}
```

## ZTP script
The ZTP script will do the following operations:

1. Install the K9SEC and MGBL packages.
2. Create a general purpose key.
3. Apply a basic configuration that allow the netconf agent to communicate with the NSO server.
4. Push the Device profile to NSO
5. Push the RSA key to NSO
6. synchronise the basic configuration with NSO

### Device profile
The device profile is described in a JSON template with the name and ip address of the device filled during the ZTP execution, the IOS-XR CLI based template looks like this:

```json
myDevice = {
    "device": {
        "name": "XXXX",
        "address": "XXXX",
        "port": 22,
        "state": {
            "admin-state": "unlocked"
        },
        "authgroup": "ios-xr-default",
        "device-type": {
            "cli": {
              "ned-id": "tailf-ned-cisco-ios-xr-id:cisco-ios-xr"
            }
        }
    }
}
```

The Netconf profile template will look like this:

```
myDevice = {
    "device": {
        "name": "XXXX",
        "address": "XXXX",
        "port": 22,
        "state": {
            "admin-state": "unlocked"
        },
        "authgroup": "ios-xr-default",
        "device-type": {
            "netconf": {
              "ned-id": "tailf-ncs-ned:netconf"
            }
        }
    }
}
```
