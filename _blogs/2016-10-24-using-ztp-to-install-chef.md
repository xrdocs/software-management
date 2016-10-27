---
published: false
date: '2016-10-24 22:24 -0700'
title: Using ZTP to install Chef
---
## Introduction
Chef is an automation platform that "turns infrastructure into code", allowing users to manage and deploy resources across multiple servers, or nodes. Chef allows users to create and download recipes (stored in cookbooks) to automate content, configuration and policies on these nodes.

Chef is comprised of a Chef server, one or more workstations, and a number of nodes that are managed by the chef-client installed on each node. You can download the [IOS-XR Chef client package](https://packages.chef.io/stable/ios_xr/6/chef-12.15.19-1.ios_xr6.x86_64.rpm "IOS-XR Chef client package") from Chef and installed directly inside the control plane LXC of IOS-XR.

Chef like Puppet (see my previous blog [Using ZTP to install Puppet](https://xrdocs.github.io/software-management/blogs/2016-10-20-using-ztp-to-install-puppet/ "here")

Automated configuration management tools play a vital role in managing complex enterprise infrastructures. Amongst the many advantages, the ones pertinent to IOS-XR and network node in general are:

**Consistency:** It makes it easier for configuration changes to meet compliance and security requirements. By automating repeated tasks (like applying a SMU), it allows network administrators to concentrate on more important stuff. 

**Efficient change management:**. Automated configuration management can remove delay when deploying new technologies, reducing the number of processes needed to manage change. Small change batches can be performed on a more regular basis.

**Availability:** Automatated configuration management tool help quickly restore service. Rather than troubleshooting an issue by hand, a system can be reset to well known working status. 

**Visibility:** Configuration management tools include auditing and reporting capabilities, changes can be automatically logged in all relevant tracking systems.

