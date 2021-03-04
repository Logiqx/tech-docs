# Windows

## Docker Desktop

### Boot Configuration - Rarely Required

To switch between Docker and VirtualBox on Windows it is necessary to enable / disable Hyper-V.

I've made some notes on how to create suitable [boot configurations](../../Boot.md).

Nowadays, I just run with Hyper-V enabled and rarely use VirtualBox.



### Engine Configuration

The Docker engine can be configured on Linux by tweaking `/etc/docker/docker.json`.

This is done through Settings -> GUI (Advanced) on Docker for Windows.



### WSL 2 Configuration

#### Memory

When I switched to WSL 2 my Windows version was 2004, build 19041.

The default memory assigned to the WSL 2 Linux VM was changed to [80% of the host memory](https://docs.microsoft.com/en-us/windows/wsl/release-notes#build-19028) in build 19028.

This is something of an issue on my 16GB machine because WSL 2 assigned 13GB to the Linux VM and [Linux eats RAM](https://www.linuxatemyram.com/).

A solution to this issue can be found in my WSL [notes](../../WSL/README.md) in the WSL configuration section.



#### Disk Space

Details about the virtual hard disks can be found in my WSL [notes](../../WSL/README.md) in the WSL configuration section.



### Removing Legacy VM and LCOW after switch to WSL2

Since Docker Desktop 2.2.0.0 / 2.3.0.2 the WSL2 integration is preferable to the traditional VM or LCOW.

The hard disks for the legacy VM can be found in `C:\%USERNAME%\Public\Documents\Hyper-V\Virtual hard disks`.

- I removed the legacy VM and the virtual hard disk via the Docker GUI; Troubleshoot -> Clean / Purge Data -> Hyper-V.
- I removed my old LCOW experiments via the Docker GUI; Troubleshoot -> Clean / Purge Data -> Windows Containers.



### Bind Mounts

When developing on Windows with WSL 2 then be sure that the bind mounts are within the Linux file system and not on the Windows partition. Bind mounts are much faster for files under /home then /mnt/c and this is especially noticeable for "git" operations.

You can also use bind mounts on Linux itself to facilitate copying of files between Linux distros as described in [tips and tricks](Tips_and_Tricks.md).

#### Legacy Setup

These were a little tricky to get working on Windows since the WSL2 implementation uses bind mounts within Linux.

The sections below provides some basic pointers as to what was required in the way of setup on my Windows machine.

##### Windows User

Create a Windows user called "Docker".

##### Share C: Drive

Right-click C: -> Properties -> Sharing -> Share... and give full access to the "Docker" user.

##### Docker For Windows

Settings -> Shared Drives -> C, providing the Docker username and password

##### Anti-Virus Exclusion

Norton -> Settings -> Firewall -> Configure Public Network Exceptions -> File and Printer Sharing



## Troubleshooting

### Upgrade Issue

I switched to WSL 2 with Docker 2.3.0.4 and after getting all of my images + containers + volumes migrated, I attempted an upgrade to 3.1.0. After the upgrade the Docker engine failed to start after 20 minutes so I shut down and went back to it the next day. The following morning the Docker engine failed to start and quickly returned an error message.

After taking backups of everything (e.g. vhdx files for Docker and WSL 2), I stumbled across a thread that suggested deleting the files in `\\wsl$\docker-desktop-data\isocache` and this actually fixed my issue. I found that there was just enough time after attempting to start Docker to get into the folder and delete the files. Docker re-created the isocache files and then fixed itself.

Quick summary:

- Upgraded from 2.3.0.4 to 3.1.0
- After the install it took ages trying to re-start Docker (circa 20 mins) and it was late at night so I did a Windows shutdown
- Docker wouldn't start the next day with an error similar to GitHub issue [8748](https://github.com/docker/for-win/issues/8748)
- wsl --list -v showed docker-desktop disappearing when trying to start Docker
- Eventually, I realised that I could go to \\wsl$\docker-desktop-data\isocache and delete files as Docker was trying to start
- The files were re-populated in the cache and the docker engine shut down cleanly
- I was then able to start Docker and from that point onwards was working fine!

Threads on GitHub:

- [Docker fails on startup on Win-10 after the update #8748](https://github.com/docker/for-win/issues/8748)

- [Update from 2.5.0.0 to 2.5.0.1 fails. Docker won't start now. #9504](https://github.com/docker/for-win/issues/9504)

I added some brief comments to say what worked for me in case they are helpful to others.



### Pengwin + Windows Terminal

I discovered that running Docker containers from Windows Terminal did not work the same as from a Pengwin window.

When I tried to start any container utilising bind mounts, I got the following error:

```
docker: Error response from daemon: invalid mount config for type "bind": bind source path does not exist:
```

For standalone containers this was pretty evident but with my Docker Compose script that started a MariaDB container it caused some very strange symptoms and the database was failing to start.

Initially I avoided Windows Terminal and stuck to a Pengwin window but I've subsequently migrated my projects to Ubuntu.

Further investigation has shown that so long as WLinux is started when Docker integration is enabled it may work ok?

To potentially fix Pengwin + Docker integration run the command `wsl --terminate WLinux` and let Docker re-start the WLinux distro.



## Linux Containers on Windows

Linux Containers on Windows (LCOW) has been an experimental feature in Docker for Windows.

However, I ran into some issues such as inability to stop Linux containers so I stuck to traditional Linux containers.

Since Docker for Windows (2.2.0.0 / 2.3.0.2) has integration with WSL2, I no longer have a specific use for LCOW.

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

