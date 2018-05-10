---
published: false
date: '2018-05-08 10:47 -0700'
title: Cable Management with ZTP
---
## Foreword
A while ago I wanted to refresh my knowledge of Python and get familar with JSON data structure. I decided to create a small project to help me verify that all the connections from and to newly inserted devices in my lab were correct and functional. The script in this blog may not be very Pythonesque :) as I was still learning Python but it does the job.

## Introduction
Cable Management is a a key element of provisioning a device, after installing the device, inserting the optics and cabling everything, we would like to know if everything has been setup correctly before starting the configuration process. In this blog I will go over the visualization part of the cable management, I will go over the verification process in part 2.

This example replies on LLDP to obtain information from the neighbors, LLDP provides the following usefull information:
1. The local/remote interfaces for each connection
2. The neighbor chassis identification
3. The neighbor device name
4. The neighbor device description (PID and SW version if the neighbor is IOS-XR)
5. The neighbor management IPv4/IPv6 address (if configured)

## Graph
Graph visualization is a way of representing structural information as diagrams of abstract graphs and networks. It has a wide range of application including database, bioinformatics software engineering and networking.

Their are multiple formats use to describe graphs: DOT, GXL, GRAPHML and multiple tools to generate and process graph. In my research, I found [Graphviz](https://www.graphviz.org/) and [Gephi](https://gephi.org/) to be the most used. Gephi used the [GEXF](https://gephi.org/gexf/format/) (Graph Exchange XML Format) language internally and includes a set of tools that can generate and/or process [various format](https://gephi.org/users/supported-graph-formats/), it also can be enhanced using plugins and found out that there is a [JSON export plugin](https://github.com/oxfordinternetinstitute/gephi-plugins/tree/jsonexporter-plugin/modules/JsonExporter).





## 

Enter text in [Markdown](http://daringfireball.net/projects/markdown/). Use the toolbar above, or click the **?** button for formatting help.
