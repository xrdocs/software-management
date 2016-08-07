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

With IOS XR 6.0, the Package Installation Envelope (PIE) format has been discarded in favor of the RPM Package Manager (RPM) format. This move aligns IOS XR 6.0  and above more closely with RPM-based Linux distribution like Red Hat or Centos. RPM is a free software project and is released under GPL. Briefly, RPMs contain the following elements:

1. CPIO archive: Contains the absolute path of all the package’s files;
2. Metadata: Contains the package dependencies;
3. Scriptlets: Scripts that perform pre and post (un)installation tasks;

The Metadata is a XML file that helps determine and resolve package dependencies, to facilitate dependencies resolution the metadata is used to poulate a small database located in /var/lib/rpm that can be queried using tools like YUM or RPM.
A Software Maintenance Update (SMU) will be published in the form of tape archive (tar) format. The SMU will contain the following files:

1. A Readme.txt file describing the content of the SMU;
2. One or more RPMs;
3. A Package-mdata.txt that contains a MD5 checksum of all the packages in the tar file;

![Anatomy of RPM]({{site.baseurl}}/images/RPM.png)

## XR Packages Installation 
Within IOS-XR, two new CLI commands have been introduced that complement the existing ones: install update and install upgrade, described Table 1. These new commands require an external packages repository accessible through FTP/SFTP/SCP/TFTP or HTTP.

| Command   | Description |
| install update source <repository> | When no package is specified, update latest SMUs of all installed packages. |
| install upgrade source <repository> version <ver_num> | Upgrade the base image to the specified version. All installed packages are upgraded to same release as the base package. |


