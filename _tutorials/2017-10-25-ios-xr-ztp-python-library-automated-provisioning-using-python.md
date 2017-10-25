---
published: true
date: '2017-10-25 12:50 -0700'
title: 'IOS-XR ZTP Python Library: Automated provisioning using python (6.2.2+)'
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

If you've not had a chance to play around with Zero Touch Provisioning (ZTP) on IOS-XR, then I would implore you take a look at the following blogs:

*  **IOS-XR ZTP: Learning through Packet Captures**: Akshat Sharma tells you everything you need to know to understand the DHCP (v4 and v6) server setup and packet captures to illustrate the DHCP options that may be used for classification of devices during provisioning:
><https://xrdocs.github.io/software-management/blogs/2017-09-21-ios-xr-ztp-learning-through-packet-captures/>

*  **Working with ZTP (bash scripts)**: Patrick Warichet walks through the ZTP workflow and illustrates examples of bash scripts for zero touch provisioning using the ZTP bash utilities that enable the bash script to interact with IOS-XR CLI.
><https://xrdocs.github.io/software-management/tutorials/2016-08-26-working-with-ztp/>

Apart from a new DHCPv4 option introduced in IOS-XR Release 6.2.25 (option 124), both the articles above are valid for IOS-XR starting from release 6.1.2.


In this tutorial we will introduce a brand new ZTP library that enables users to write ZTP scripts directly in python without having to create wrappers around the native bash utilities available to interact with IOS-XR CLI ( The bash utilities are explained here: ([ZTP bash utilties])(https://xrdocs.github.io/software-management/tutorials/2016-08-26-working-with-ztp/#ztp-utilities)

Further, we'll go a step further and write an exhaustive ZTP script in python that handles XR config replace, installation of packages in XR, spin up a docker container and install puppet - all on a dual RP NCS5500. 

Let's get started!
{: .notice--success} 

## 

