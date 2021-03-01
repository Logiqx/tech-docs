# Windows

## Docker Desktop

### Boot Configuration

To switch between Docker and VirtualBox on Windows it is necessary to enable / disable Hyper-V.

I've made some notes on how to create suitable [boot configurations](../../Boot.md).



### Bind Mounts

These can be a little tricky to get working on Windows.

The section below provides some basic pointers as to what is required in the way of setup.

#### Setup

##### Docker For Windows

Settings -> Shared Drives -> C

##### Share C: Drive

Right-click C: -> Properties -> Sharing -> Share...

##### Anti-Virus Exclusion

Norton -> Settings -> Firewall -> Configure Public Network Exceptions -> File and Printer Sharing



### Engine Configuration

The Docker engine can be configured on Linux by tweaking `/etc/docker/docker.json`.

This is done through Settings -> GUI (Advanced) on Docker for Windows.



## Linux Containers on Windows

Linux Containers on Windows (LCOW) is an experimental feature in Docker for Windows.

Until some of the issues with it have been fixed, I will continue to run traditional Linux containers.

I've made some notes on my experiences with [LCOW](LCOW.md).



## Reference

### Docker Toolbox (Legacy)

Docker Toolbox - [Docker Toolbox overview](https://docs.docker.com/toolbox/overview/)

Docker Machine - [Get started with Docker Machine and a local VM](https://docs.docker.com/machine/get-started/)

### Microsoft

Microsoft - [Containers on Windows](https://docs.microsoft.com/en-us/virtualization/windowscontainers/about/)

Microsoft Azure (Mark Russinovich) - [Containers: Docker, Windows and Trends](https://azure.microsoft.com/en-us/blog/containers-docker-windows-and-trends/)

Microsoft Azure - [Nested Virtualization in Azure](https://azure.microsoft.com/en-gb/blog/nested-virtualization-in-azure/)

### Hyper-V

InfoWorld - [What you need to know about Docker in Windows](https://www.infoworld.com/article/3163257/application-development/what-you-need-to-know-about-docker-in-windows.html) - e.g. Server Core vs Windows Nano

TechTarget - [Differences between Windows Server Containers, Hyper-V Containers and VMs](http://searchservervirtualization.techtarget.com/tip/Differences-between-Windows-Server-Containers-Hyper-V-Containers-and-VMs)

ITPro Today - [Differences between Windows Containers and Hyper-V Containers in Windows Server 2016](http://www.itprotoday.com/windows-8/differences-between-windows-containers-and-hyper-v-containers-windows-server-2016)

Cloudstacking - [Running Hyper-V VMware or Xen on an AWS EC2 Instance?](http://cloudstacking.com/posts/running-hyper-v-vmware-or-xen-on-an-aws-ec2-instance.html)

