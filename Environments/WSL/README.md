# Windows Subsystem for Linux (WSL)

## Background

[Windows Subsystem for Linux](https://docs.microsoft.com/en-us/windows/wsl/about)

[Windows 10 WSL vs. Linux Performance For Early 2018](https://www.phoronix.com/scan.php?page=article&item=wsl-february-2018&num=1)



## Ubuntu

I started off using the Ubuntu distribution from the Microsoft Store.

Upgraded using the following commands:

```sh
sudo -- sh -c 'apt-get update; apt-get upgrade -y; apt-get dist-upgrade -y; apt-get autoremove -y; apt-get autoclean -y'
```



## WLinux

In March 2019, I switched to the WLinux distribution from the Microsoft Store.

The most appealing feature of WLinux is the inbuilt Docker bridge, integrating with Docker for Windows.



Notes:

Run later - fcitx-config-gtk3

Run again - wlinux-setup



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

