# Windows Subsystem for Linux (WSL)

## Background

Here are a couple of links to get started:

- [Windows Subsystem for Linux](https://docs.microsoft.com/en-us/windows/wsl/about)
- [Windows 10 WSL vs. Linux Performance For Early 2018](https://www.phoronix.com/scan.php?page=article&item=wsl-february-2018&num=1)

I used WSL initially but subsequently switched to WSL 2 due to the integrated support in Docker.



## Tips

### Useful Commands

You can quickly open Windows Explorer from any Linux folder using `explorer.exe .` (do not forget the dot).



## Linux Distros

### Ubuntu

I started off using the Ubuntu distribution from the Microsoft Store but switched to WLinux/PengWin for the inbuilt Docker bridge.

Thorough system upgrades can be achieved using the following commands:

```sh
sudo -- sh -c 'apt-get update; apt-get upgrade -y; apt-get dist-upgrade -y; apt-get autoremove -y; apt-get autoclean -y'
```

Docker Desktop Community 2.3.0.2 added integrated support for WSL 2 in Windows Home so I switched back to Ubuntu for my project work.

Another benefit is that the Docker client in Ubuntu is automatically upgraded by Docker Desktop, unlike the one in Pengwin.



### Pengwin

In March 2019, I started using the Pengwin distribution (formerly known as WLinux) from the [Microsoft Store](https://www.microsoft.com/en-gb/p/wlinux/9nv1gv1pxz6p).

The most appealing feature of WLinux was the inbuilt Docker bridge, integrating with Docker for Windows.

System updates can be performed as follows, rather than using "apt-get" directly:

```
pengwin-setup update
pengwin-setup upgrade
```

I had problems with Docker 3.1 + Pengwin + Windows Terminal which are described in my Docker for [Windows](../Containers/Docker/Windows.md) document. I also found that it was impossible to auto-start MariaDB via Docker Compose and I had a suspicion that it was related to the Pengwin integration.

Since Docker added integrated support for WSL 2 all of the other Linux distros have the equivalent feature so I have switched back to Ubunutu. The above problems were a couple of reasons for switching back to Ubuntu.

Notes:

- On my personal laptop WLinux renamed itself to Pengwin on 11 April 2019.
- The initial setup / configuration can be re-run via the "pengwin-setup" command, formerly "wlinux-setup".
- I never got around to running "fcitx-config-gtk3".



## WSL Monitoring

### Distros

The installed Linux distros and their run state can be viewed using the command `wsl --list -v`.

Individual distros can be terminated using `wsl --terminate [distro name]`.

All distros and the shared VM can be shutdown using `wsl --shutdown`.

### Virtual Machine

The virtual machine being used for WSL 2 can be seen via the command `hcsdiag list`.

To run this command you must be an administrator or in the "Hyper-V Administrators" group. If you add your user to the "Hyper-V Administrators" group (e.g. via Computer Management) you will need to logout and login for it to become effective.

Whilst it is possible to kill the VM using `hcsdiag kill`, I prefer to use `wsl --shutdown` which feels cleaner.

Note: Shutting down WSL 2 (including the VM) is essential, prior to using commands such as `diskpart` or you risk corrupting disk images.

### Docker Integration

PengWin (formerly WLinux) continues to use a socat process:

```
socat UNIX-LISTEN:/var/run/docker.sock,fork,group=docker,umask=007 EXEC:\'/mnt/c/Users/Mike/.npiperelay/npiperelay.exe\' -ep -s //./pipe/docker_engine,nofork
```

Ubuntu uses features built into Docker Desktop:

```
docker serve --address unix:///home/mike/.docker/run/docker-cli-api.sock

/mnt/wsl/docker-desktop/docker-desktop-proxy --distro-name Ubuntu --docker-desktop-root /mnt/wsl/docker-desktop --use-cloud-cli=true
```

I've already mentioned that I had problems with Docker 3.1 + Pengwin + Windows Terminal and I suspect it is something to do with the implementation of bind mounts. This was potentially responsible for my trouble in starting MariaDB using Docker Compose. The socket integration is clearly quite different (see above) and bind mounts may differ as well, so I decided to go with a mainstream Linux release.



## WSL Configuration

WSL was cool but WSL 2 is vastly superior. There are however a couple of pitfalls relating to **memory** and **disk usage**.

### Memory

When I switched to WSL 2 my Windows version was 2004, build 19041.

The default memory assigned to the WSL 2 Linux VM was changed to [80% of the host memory](https://docs.microsoft.com/en-us/windows/wsl/release-notes#build-19028) in build 19028.

This is something of an issue on my 16GB machine because WSL 2 assigned 13GB to the Linux VM and [Linux eats RAM](https://www.linuxatemyram.com/).

I originally found a solution in the [comments](https://github.com/microsoft/WSL/issues/4166#issuecomment-526725261) of a GitHub issue. The fix is to limit the WSL 2 memory using [.wslconfig](https://docs.microsoft.com/en-us/windows/wsl/wsl-config#configure-global-options-with-wslconfig):

```
[WSL 2]
memory=8GB
```

You can check that the limit has been implemented using the command `free -h`. It will show roughly the same figures from all of your WSL 2 Linux distros.

It should be noted that the default has subsequently been changed in Windows build 20175:

- "[Adjust default memory assignment of WSL 2 VM to be 50% of host memory or 8GB, whichever is less](https://docs.microsoft.com/en-us/windows/wsl/release-notes#build-20175)"

Note that it references the GitHub issue 4166 where I found the above fix.

If you want to clear the page cache then have a look at the [Linux](../../Operating_Systems/Linux/README.md) notes.



### Disk Space

Potentially need to be careful about disk space of the virtual hard disks:

- copying large folders
- creating large zips / backups

The expanding vhdx files do not automatically shrink, although I believe that empty space is re-used. This will potentially fill up the host disk and potentially affect future backup sizes if the vhdx files are being directly backed up.

A convenient way to locate the vhdx files is using via a Linux prompt, although similar can be done with a Windows search:

```
cd /mnt/c/Users/Mike/AppData/Local/
find . -name '*.vhdx' 2>/dev/null
```

The file sizes can then be determined as follows:

```
cd /mnt/c/Users/Mike/AppData/Local/
du -sh Packages/*/LocalState/*.vhdx Docker/wsl/*/*.vhdx | sort -rh
```

When using Windows 10 home, vhdx files can be shrunk using a procedure described in a [comment](https://github.com/microsoft/WSL/issues/4699#issuecomment-627133168) of a GitHub issue:

1. Quit Docker Desktop via the Windows GUI.
2. Terminate the WSL 2 distros and shutdown the VM using ```wsl --shutdown```.
3. Compact the virtual hard disk using the [diskpart](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/diskpart) command, being sure to use a fully qualified path name.

```
select vdisk file="C:\Users\Mike\AppData\Local\Packages\WhitewaterFoundryLtd.Co.16571368D6CFF_kd1vv0z0vy70w\LocalState\ext4.vhdx"
attach vdisk readonly
compact vdisk
detach vdisk
exit
```

Note: Should it be necessary to expand the maximum size beyond 256GB then it is a simple [procedure](https://docs.microsoft.com/en-us/windows/wsl/compare-versions#understanding-wsl-2-uses-a-vhd-and-what-to-do-if-you-reach-its-max-size) involving the diskpart command and some Linux commands.



## Linux Tools

### APT

Advanced Package Tool (APT) - [Wiki](https://en.wikipedia.org/wiki/APT_(Debian))

[Difference Between apt and apt-get Explained](https://itsfoss.com/apt-vs-apt-get-difference/)



Install htop:

```sh
sudo apt install htop
```

Install Midnight Commander:

```sh
sudo apt install mc
```

Install GCC:

```sh
sudo apt install gcc
```

Install make:

```sh
sudo apt install make
```

Install CVS:

```sh
sudo apt install cvs
```



Install asciidoc, required for cvs-fast-export:

```sh
sudo apt-get update 		# This was needed for asciidoc to install successfully
sudo apt install asciidoc
```

Build and install  [cvs-fast-export](http://www.catb.org/~esr/cvs-fast-export/):

```sh
mike@MIKE-ENVY:/mnt/c/Users/Mike/Downloads/_cvs_/cvs-fast-export-1.45$ make
...

mike@MIKE-ENVY:/mnt/c/Users/Mike/Downloads/_cvs_/cvs-fast-export-1.45$ make check
...

mike@MIKE-ENVY:/mnt/c/Users/Mike/Downloads/_cvs_/cvs-fast-export-1.45$ sudo make install
[sudo] password for mike:
install -d "/usr/local/bin"
install cvs-fast-export cvssync cvsconvert "/usr/local/bin"
install -d "/usr/local/share/man/man1"
install -m 644 cvs-fast-export.1 "/usr/local/share/man/man1"
install -m 644 cvssync.1 "/usr/local/share/man/man1"
install -m 644 cvsconvert.1 "/usr/local/share/man/man1"
```



Install Go, required for reposurgeon:

```sh
sudo apt install golang
```

Install PyPy, required for reposurgeon:

```sh
sudo apt install pypy
```

Install PyLint, required for reposurgeon:

```sh
sudo apt install pylint
```

Install Subversion, required for reposurgeon:

```sh
sudo apt install subversion
```

Build and install  [reposurgeon](http://www.catb.org/~esr/reposurgeon/):

```sh
mike@MIKE-ENVY:/mnt/c/Users/Mike/Downloads/_cvs_/reposurgeon-3.45$ make
...

mike@MIKE-ENVY:/mnt/c/Users/Mike/Downloads/_cvs_/reposurgeon-3.45$ sudo make install
GOPATH=/mnt/c/Users/Mike/Downloads/_cvs_/reposurgeon-3.45 go build repocutter
GOPATH=/mnt/c/Users/Mike/Downloads/_cvs_/reposurgeon-3.45 go build repomapper
install -d "/usr/local/bin"
install -d "/usr/local/share/doc/reposurgeon"
install -d "/usr/local/share/man/man1"
install -m 755 reposurgeon repotool repomapper repocutter "/usr/local/bin"
install -m 644 README.adoc NEWS TODO reposurgeon-git-aliases reposurgeon.html repotool.html repomapper.html repocutter.html dvcs-migration-guide.html features.html reporting-bugs.html "/usr/local/share/doc/reposurgeon"
install -m 644 reposurgeon.1 repotool.1 repomapper.1 repocutter.1 "/usr/local/share/man/man1"
```

Author mapping

```
Mike <Mike>
Logiqx <software@mikeg.me.uk>

```



Filesystem:

C:\Users\Mike\AppData\Local\Packages\CanonicalGroupLimited.UbuntuonWindows_79rhkp1fndgsc\LocalState\rootfs



Core tools: apt, sed, grep, awk, top, tmux, ssh, scp, etc.
Shells: Bash, zsh, fish, etc.
Dev tools: vim, emacs, nano, git, gdb, etc.
Languages & platforms: Node.js & npm, Ruby & Gems, Java & Maven, Python & Pip, C/C++, C# & .NET Core & Nuget, Go, Rust, Haskell, Elixir/Erlang, etc.
Systems & Services: sshd, Apache, lighttpd, nginx, MySQL, PostgreSQL



## Services

### LAMP

I've decided not to run LAMP on top of WSL as I am using Docker:

[How to Install LAMP Stack Server on Windows Subsystem Linux (WSL Windows 10)](https://medium.com/@ssharizal/how-to-install-lamp-stack-server-on-windows-subsystem-linux-wsl-windows-10-133419c22473)

