# Windows Subsystem for Linux (WSL)

## Background

[Windows Subsystem for Linux](https://docs.microsoft.com/en-us/windows/wsl/about)

[Windows 10 WSL vs. Linux Performance For Early 2018](https://www.phoronix.com/scan.php?page=article&item=wsl-february-2018&num=1)



## Ubuntu

I started off using the Ubuntu distribution from the Microsoft Store but switched to WLinux/PengWin for the inbuilt Docker bridge.

Thorough system upgrades can be achieved using the following commands:

```sh
sudo -- sh -c 'apt-get update; apt-get upgrade -y; apt-get dist-upgrade -y; apt-get autoremove -y; apt-get autoclean -y'
```

Note: Docker Desktop Community 2.3.0.2 added integrated support for WSL2 so I switched back from using Pengwin to Ubuntu.



## Pengwin

In March 2019, I started using the Pengwin distribution (formerly known as WLinux) from the [Microsoft Store](https://www.microsoft.com/en-gb/p/wlinux/9nv1gv1pxz6p).

The most appealing feature of WLinux was the inbuilt Docker bridge, integrating with Docker for Windows.

System updates can be performed as follows, rather than using "apt-get" directly:

```
pengwin-setup update
pengwin-setup upgrade
```

Since Docker added integrated support for WSL2 all of the other Linux distros have the equivalent feature.

Notes:

- On my machine WLinux renamed itself to Pengwin on 11 April 2019.
- The initial setup / configuration can be re-run via the "pengwin-setup" command, formerly "wlinux-setup".
- I never got around to running "fcitx-config-gtk3".



## Configuration

WSL was cool but WSL2 is vastly superior. There are however a couple of pitfalls relating to **memory** and **disk usage**.

### Memory

When I switched to WSL2 my Windows version was 2004, build 19041.

The default memory assigned to the WSL2 Linux VM was changed to [80% of the host memory](https://docs.microsoft.com/en-us/windows/wsl/release-notes#build-19028) in build 19028.

This is something of an issue on my 16GB machine because WSL2 assigned 13GB to the Linux VM and [Linux eats RAM](https://www.linuxatemyram.com/).

I originally found a solution in the [comments](https://github.com/microsoft/WSL/issues/4166#issuecomment-526725261) of a GitHub issue. The fix is to limit the WSL2 memory using [.wslconfig](https://docs.microsoft.com/en-us/windows/wsl/wsl-config#configure-global-options-with-wslconfig):

```
[wsl2]
memory=8GB
```

You can check that the limit has been implemented using the command `free -h`. It will show roughly the same figures from all of your WSL2 Linux distros.

It should be noted that the default has subsequently been changed in Windows build 20175:

- "[Adjust default memory assignment of WSL2 VM to be 50% of host memory or 8GB, whichever is less](https://docs.microsoft.com/en-us/windows/wsl/release-notes#build-20175)"

Note that it references the GitHub issue 4166 where I found the above fix.

If you want to clear the page cache then have a look at the [Linux](../../Operating_Systems/Linux/README.md) notes.



## Tools

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

