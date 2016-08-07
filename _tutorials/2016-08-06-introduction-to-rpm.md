---
published: true
date: '2016-08-06 17:06 -0700'
title: Introduction to RPM
author: pwariche
excerpt: Introduction to RPM Package Manager
tags:
  - iosxr
  - linux
---
## Introduction

With IOS XR 6.0, the Package Installation Envelope (PIE) format has been discarded in favor of the RPM Package Manager (RPM) format. This move aligns IOS XR 6.0 more closely with RPM-based Linux distribution like Red Hat or Centos. RPM is a free software project and is released under GPL. Briefly, RPMs contain the following elements:

1. CPIO archive: Contains the absolute path of all the package’s files;
2. Metadata: Contains the package dependencies;
3. Scriptlets: Scripts that perform pre and post (un)installation tasks;

The Metadata is a XML file that helps determine and resolve package dependencies, to facilitate dependencies resolution package content are filled in a small database located in /var/lib/rpm that can be queried using tools like YUM or RPM.
A Software Maintenance Update (SMU) will be published in the form of tape archive (tar) format. The SMU will contain the following files:

A Readme.txt file describing the content of the SMU
One or more RPMs
A Package-mdata.txt that contains a MD5 checksum of all the packages in the tar file.

![Anatomy of RPM]({{site.baseurl}}/images/RPM.png)
