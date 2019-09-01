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

## Preparation of UCS servers
As was told before, I had UCS C220 M4S servers, that were used for different purposes. As an example let's take CMS1000 server, which has two physical drives and one virtual drive configured in RAID 1.

[![alt text](/img/2019-05-05-vsan-on-ucs/Virtual-drive-info.png "CMS1000 physical drives")](https://raw.githubusercontent.com/dmkravch/dmkravch.github.io/master/img/2019-05-05-vsan-on-ucs/Virtual-drive-info.png)

[![alt text](/img/2019-05-05-vsan-on-ucs/Virtual-drive-info.png "CMS1000 virtual drives")](https://raw.githubusercontent.com/dmkravch/dmkravch.github.io/master/img/2019-05-05-vsan-on-ucs/Virtual-drive-info.png)

In this case I needed to re-configure to RAID 0 and create 2 disks (one for cache and one as permanent storage). So, at the beginning, it's needed to "Clear all Configuration" via the CIMC Web GUI:
[![alt text](/img/2019-05-05-vsan-on-ucs/Clear-all.png "Clear all Configuration")](https://raw.githubusercontent.com/dmkravch/dmkravch.github.io/master/img/2019-05-05-vsan-on-ucs/Clear-all.png)

And, I've also decided to create a separate disk for ESXi installer (Create Virtual Drive from Unused Physical Drives):
[![alt text](/img/2019-05-05-vsan-on-ucs/Create-Virtual-Drive-for-ESXi.png "Create Virtual Drive from Unused Physical Drives")](https://raw.githubusercontent.com/dmkravch/dmkravch.github.io/master/img/2019-05-05-vsan-on-ucs/Create-Virtual-Drive-for-ESXi.png)

I've repeated the same for the cache (SSD) and storage drives (with respective sizes):
[![alt text](/img/2019-05-05-vsan-on-ucs/created-drives.png "Created Drives")](https://raw.githubusercontent.com/dmkravch/dmkravch.github.io/master/img/2019-05-05-vsan-on-ucs/created-drives.png)

Now we need to prepare the Virtual disks for installing ESXi and for being part of vSAN by removing everything and clearing all existing partitions that might be there:
> **NOTE**: Initialize – that will delete all the information and prepare the virtual disks

[![alt text](/img/2019-05-05-vsan-on-ucs/Initialieze.png "Initialize")](https://raw.githubusercontent.com/dmkravch/dmkravch.github.io/master/img/2019-05-05-vsan-on-ucs/Initialieze.png)

That needs to be repeated with every Virtual Disk. And the same procedure also needs to be performed with the remaining two UCS servers to prepare them as part of the vSAN cluster.

## Configuration of the vSAN
As a preliminary step, ESXi needs to be installed onto the small ESXi virtual drive that we created. Feel free to use any available guide or official pages. For example this link: [How to install ESXi 6.7 step-by-step](https://masteringvmware.com/how-to-install-esxi-6-7-step-by-step/) or official document: [VMware ESXi Installation and Setup](https://docs.vmware.com/en/VMware-vSphere/6.7/vsphere-esxi-67-installation-setup-guide.pdf).

After the installation and adding that host to be managed by the vCenter, you will be able to mark a disk as Flash (it's kind of masking HDD and presenting it as SSD, so vSAN could be created): [Mark Storage Devices as Flash](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.storage.doc/GUID-99BB81AC-5342-45E5-BF67-8D43647FAD31.html).

In my case, I was able to mark the disks as Flash, so they could be part of the vSAN. Also, make sure that on every disk you are adding there is no partitions created:
[![alt text](/img/2019-05-05-vsan-on-ucs/mark-as-flash.png "Marked as Flash and Partitions")](https://raw.githubusercontent.com/dmkravch/dmkravch.github.io/master/img/2019-05-05-vsan-on-ucs/mark-as-flash.png)

As a result, I had 3 hosts, with 3 disks each. And on each of the hosts 2 disks were used as part of the vSAN, so, it total 6 disks.

Below, you could see that vCenter recognizes that my (in fact HDD) disk could be used as Flash (meaning for Cache purposes):
[![alt text](/img/2019-05-05-vsan-on-ucs/disk_as_flash.png "Disks discovered as flash")](https://raw.githubusercontent.com/dmkravch/dmkravch.github.io/master/img/2019-05-05-vsan-on-ucs/disk_as_flash.png)

> **NOTE**: Unfortunately, I didn't make any screenshots of my exact configuration, and during the time of writing of this post I've already dismounted and removed the configuration of vSAN. But could tell you that it behaved as a regular shared storage. I could perform vMotion of virtual machines, and still use the vSAN datastore.

For the exact steps on how to configure vSAN, please, refer to [Configure a Cluster for vSAN Using the vSphere Web Client](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vsan-planning.doc/GUID-DC33632F-E458-4C8F-B0A8-D9492AF16512.html).

## Conclusion
As a conclusion, I could say that it's possible to practice vSAN even if you don't have appropriate licenses, SSD drive and disks of the same size. Definitely, performance will be degraded, but still it's enough to get familiar with the vSAN and run some virtual machines on it.

Thank you for reading. 
