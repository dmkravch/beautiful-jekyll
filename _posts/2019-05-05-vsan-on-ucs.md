---
layout: post
title: How to configure vSAN on Cisco UCS
subtitle: This post will explain requirements and how to configure VMware vSAN on Cisco UCS servers.
tags: [vmware, ucs, configuration]
---
> **WARNING**: First of all, I want to point out that this is not a Production recommended deployment, this was solely done to get familiar with the vSAN feature and prepare to the VCP exam.

For our lab we got a couple of UCS C220 M4S servers. Some of them were Cisco BE6000 and some CMS1000.

The CMS1000 servers have a lot of CPU and RAM, but relatively small disk space (278 GB). We don't have a shared physical storage.

I will be discussing the requirements for the vSAN 6.7.

Requirements for the vSAN:
* Minimum 3 ESXi hosts to create a clusters
* Each ESXi host in a cluster must contain one flash drive for cache and one either flash or HDD drive for persistent storage.
From the official documentation it sounds like: One SAS or SATA solid-state disk (SSD) or PCIe flash device; One SAS or SATA host bus adapter (HBA), or a RAID controller that is in passthrough mode or RAID 0 mode;
* IPv4 or IPv6 network connectivity

Licensing:
* Using vSAN in production environments requires a special license that you assign to the vSAN clusters.

> **NOTE**: In my case, during the initial deployment, I missed the fact that I might require a license to use vSAN. Fortunately, I figured out that creation of a new Cluster will allow me to use vSAN in a grace period, which were pretty enough to get familiar with the vSAN.
