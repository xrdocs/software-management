---
published: false
date: '2016-10-24 22:24 -0700'
title: Using ZTP to install Chef
author: Patrick Warichet
excerpt: Installing Chef with Zero Touch Provisioning
tags:
  - iosxr
  - cisco
---
## Introduction
Chef is an automation platform that "turns infrastructure into code", allowing users to manage and deploy resources across multiple servers, or nodes. Chef allows users to create and download recipes (stored in cookbooks) to automate content, configuration and policies on these nodes.

Chef is comprised of a Chef server, one or more workstations, and a number of nodes that are managed by the chef-client installed on each node. You can download the [IOS-XR Chef client package](https://packages.chef.io/stable/ios_xr/6/chef-12.15.19-1.ios_xr6.x86_64.rpm "IOS-XR Chef client package") from Chef and installed directly inside the control plane LXC of IOS-XR.

Chef like Puppet (see my previous blog [Using ZTP to install Puppet](https://xrdocs.github.io/software-management/blogs/2016-10-20-using-ztp-to-install-puppet/ "Using ZTP to install Puppet"))

Automated configuration management tools play a vital role in managing complex enterprise infrastructures. Amongst the many advantages, the ones pertinent to IOS-XR and network node in general are:

**Consistency:** It makes it easier for configuration changes to meet compliance and security requirements. By automating repeated tasks (like applying a SMU), it allows network administrators to concentrate on more important stuff. 

**Efficient change management:**. Automated configuration management can remove delay when deploying new technologies, reducing the number of processes needed to manage change. Small change batches can be performed on a more regular basis.

**Availability:** Automatated configuration management tool help quickly restore service. Rather than troubleshooting an issue by hand, a system can be reset to well known working status. 

**Visibility:** Configuration management tools include auditing and reporting capabilities, changes can be automatically logged in all relevant tracking systems.

## Chef Infrastructure
Chef requires the follwing components a server, one or more workastation and one or more nodes, The instruction below used Ubuntu Xenial for both the server and the workstation to mange the IOS-XR nodes.
### Installing the Chef Server

The Chef server is the central place that govern interaction between all workstations and managed nodes. Changes made in the workstations are uploaded to the Chef server, which is then accessed by the chef-client and used to configure individual nodes.
Installing Chef Server is easy as 1-2-3:

1 Download the latest Chef server for your favorite distro [ http://downloads.chef.io/chef-server/.]( http://downloads.chef.io/chef-server/.):
Example for Ubuntu Xenial(Chef version 12.9.1) 

```
wget https://packages.chef.io/stable/ubuntu/16.04/chef-server-core_12.9.1-1_amd64.deb
```
2 Install the server:

```
sudo dpkg -i chef-server-core_*.deb
```
3 Run the chef-server-ctl command to start the Chef server services:

```
sudo chef-server-ctl reconfigure
```

### Create a User and Organization

1 In order to link workstations and nodes to the Chef server, an administrator and an organization need to be created with associated RSA private keys. create a directory to store the keys:

```
mkdir ~/chef-keys
```
2 Create an administrator. Change username to your desired username, firstname and lastname to your first and last name, email to your email, password to a secure password, and username.pem to your username followed by .pem:

```
sudo chef-server-ctl user-create username firstname lastname email password --filename ~/chef-keys/username.pem
```
3 Create an organization. The shortname value should be a basic identifier for your organization with no spaces, whereas the fullname can be the full, proper name of the organization. The association_user value username refers to the username made in the step above:

```
sudo chef-server-ctl org-create shortname fullname --association_user username --filename ~/chef-keys/shortname.pem
```
With the Chef server installed and the needed RSA keys generated, you can move on to configuring your workstation, where all major work will be performed for your Chef’s nodes.

### Installing and Setting up the Chef Workstation

Your Chef workstation will be where you create and configure any recipes, cookbooks, attributes, and other changes made to your Chef configurations. Although this can be the same machine that host the server, it is recommended to keep the server and the workstation seperated.

1 Download the latest [Chef Development Kit](https://downloads.chef.io/chef-dk/ "Chef Development Kit"):

```
wget https://packages.chef.io/stable/ubuntu/12.04/chefdk_0.19.6-1_amd64.deb
```
2 Install ChefDK:

```
sudo dpkg -i chefdk_*.deb
```
3 Verify the components of the development kit:

```
chef verify
```
cisco@magoo-6:~$ chef verify
Running verification for component 'berkshelf'
Running verification for component 'test-kitchen'
Running verification for component 'tk-policyfile-provisioner'
Running verification for component 'chef-client'
Running verification for component 'chef-dk'
Running verification for component 'chef-provisioning'
Running verification for component 'chefspec'
Running verification for component 'generated-cookbooks-pass-chefspec'
Running verification for component 'rubocop'
Running verification for component 'fauxhai'
Running verification for component 'knife-spork'
Running verification for component 'kitchen-vagrant'
Running verification for component 'package installation'
Running verification for component 'openssl'
Running verification for component 'inspec'
Running verification for component 'delivery-cli'
Running verification for component 'git'
Running verification for component 'opscode-pushy-client'
Running verification for component 'chef-sugar'

Verification of component 'test-kitchen' succeeded.
Verification of component 'chef-dk' succeeded.
Verification of component 'chefspec' succeeded.
Verification of component 'rubocop' succeeded.
Verification of component 'knife-spork' succeeded.
Verification of component 'openssl' succeeded.
Verification of component 'delivery-cli' succeeded.
Verification of component 'opscode-pushy-client' succeeded.
Verification of component 'berkshelf' succeeded.
Verification of component 'fauxhai' succeeded.
Verification of component 'inspec' succeeded.
Verification of component 'chef-sugar' succeeded.
Verification of component 'tk-policyfile-provisioner' succeeded.
Verification of component 'chef-provisioning' succeeded.
Verification of component 'kitchen-vagrant' succeeded.
Verification of component 'git' succeeded.
Verification of component 'chef-client' succeeded.
Verification of component 'package installation' succeeded.
Verification of component 'generated-cookbooks-pass-chefspec' succeeded.
cisco@magoo-6:~$
```
