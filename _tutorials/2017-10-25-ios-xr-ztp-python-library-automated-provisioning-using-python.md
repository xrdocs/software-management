---
published: true
date: '2017-10-25 12:50 -0700'
title: >-
  IOS-XR ZTP Python Library: Automated provisioning using python (6.2.25+) -
  Part 1 
author: Akshat Sharma
excerpt: >-
  Understand the Python ZTP library in IOS-XR, the available methods and build
  your own python based provisioning scripts for IOS-XR platforms.
position: hidden
tags:
  - iosxr
  - cisco
  - linux
  - ztp
  - python
  - CLI
  - automate
  - provision
  - dhcp
---

{% include toc icon="table" title="IOS-XR ZTP Python" %}
{% include base_path %}

## Introduction

If you've not had a chance to play around with Zero Touch Provisioning (ZTP) on IOS-XR, then I would implore you to take a look at the following blogs:

*  **IOS-XR ZTP: Learning through Packet Captures**: Akshat Sharma tells you everything you need to know to understand the DHCP (v4 and v6) server setup and packet captures to illustrate the DHCP options that may be used for classification of devices during provisioning:
><https://xrdocs.github.io/software-management/blogs/2017-09-21-ios-xr-ztp-learning-through-packet-captures/>

*  **Working with ZTP (bash scripts)**: Patrick Warichet walks through the ZTP workflow and illustrates examples of bash scripts for zero touch provisioning using the ZTP bash utilities that enable the bash script to interact with IOS-XR CLI.
><https://xrdocs.github.io/software-management/tutorials/2016-08-26-working-with-ztp/>

Apart from a new DHCPv4 option introduced in IOS-XR Release 6.2.25 (option 124), both the articles above are valid for IOS-XR starting from release 6.1.2.


In Part 1 of this tutorial we will introduce a new ZTP library released as part of IOS-XR 6.2.25 that enables users to write ZTP scripts directly in python without having to create wrappers around the native bash utilities (The bash utilities were introduced in XR Release 6.1.2 and are explained here : ([ZTP bash utilties](https://xrdocs.github.io/software-management/tutorials/2016-08-26-working-with-ztp/#ztp-utilities)). We will finish this tutorial with a simple python ZTP script that will create a root user in XR and exercise some of the methods available in the library. 

In part 2 of this tutorial, we'll go a step further and write a much more exhaustive ZTP script, in python, that handles:
  * Installation of packages in XR : k9sec and mgbl RPMs
  * IOS-XR config replace
  * Spin up a docker container 
  * install puppet
  * Set up a cron job
  * All on a dual RP NCS5500 - so the ZTP script will do initial checkpointing of linux packages and files for us!


**Let's get started!**
{: .notice--success}


## NCS5508 ZTP setup


We will be using an NCS5508 router running IOS-XR 6.2.25 CCO image. Bear in mind that the script we write will work perfectly fine on xrv9k (virtual IOS-XR) as well. This tutorial focuses on NCS5508 to have a dual RP setup to play with. 









