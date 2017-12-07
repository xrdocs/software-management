---
published: false
date: '2017-12-06 16:12 -0800'
title: IOS-XR NSO Integration Part 1 (Using PnP)
---
# Introduction
Network Services Orchestrator provides end-to-end orchestration that 
spans multiple domains in your network.

Using strict, standardized YANG models for both services and devices and a highly efficient abstraction layer between your network services and the underlying infrastructure, the orchestrator lets you automate all the multivendor devices.

## PnP

The NSO PnP Server is a NSO Component which enables management of Cisco PnP clients. The NSO PnP Server provides the Cisco PnP clients with configuration, typically configuration needed at the first boot of the client (Day-0 configuration).

## ZTP
ZTP does not currently include PnP capabilities, in this article we describes a method to use the ZTP feature of IOS XR and Cisco PnP to automatically deploy a Day-0 configuration and register a device to NSO during its initial boot. NSO will then be able to initiate a connection to the device (through a Netconf NED or a CLI NED), execute a sync-from command and deploy a service thanks to reactive FASTMAP feature.

ZTP allows you download and execute a shell script at first boot. This script (pre-agg.sh in our example) will do the following tasks:

*Download and load a PnP Agent container on the device using Docker
*Creating a configuration for the agent, apply XR configuration commands required
*Run the container

The PnP Agent will be in charge of notifying to NSO that the device has booted, doing a 4-way handshake. The PnP Server will first send a Day-0 configuration, the agent will apply it and the server will then register the device into NSO CDB. After the third PnP Work Request, the PnP Server package triggers a sync-from and the reactive FASTMAP mechanism to deploy services.
