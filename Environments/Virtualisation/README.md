# Virtualisation

## VirtualBox

I've used VirtualBox since about 2010, primarily for the development of server applications for Windows (Visual Studio + SQL Server) and website development (Apache, MySQL, PHP).

I've started to make a few notes on [VirtualBox](VirtualBox.md).

Nowadays, I rarely use VirtualBox and all of my server applications are running in Docker.



## Hyper-V

To switch between Docker and VirtualBox it is necessary to [enable / disable](../Boot.md) Hyper-V.

Reboots are somewhat inconvenient so I've stopped using VirtualBox on a regular basis and tend to use Hyper-V.

Lightweight Hyper-V machines are also intrinsic to WSL2 and Docker for Windows so nowadays, I usually have Hyper-V enabled.

The virtual hard disks for VMs can be found in `C:\%USERNAME%\Public\Documents\Hyper-V\Virtual hard disks`

I removed the legacy Docker VM and the virtual hard disk via the Docker GUI; Troubleshoot -> Clean / Purge Data -> Hyper-V.



## Vagrant

Vagrant - https://www.vagrantup.com/

Vagrant by HashiCorp enables users to create and configure lightweight, reproducible, and portable development environments.

Vagrant is software that is used to manage a development environment. Through the command line, you can grab any available OS, install it, configure it, run it, work inside of it, shut it down, and more. Using VirtualBox and Vagrant, you can simulate the production environment of your app or website.



## Packer

Packer - https://www.packer.io/

Packer by HashiCorp is a free and open source tool for creating golden images for multiple platforms from a single source configuration.

Packer is easy to use and automates the creation of any type of machine image. It embraces modern configuration management by encouraging you to use a framework such as Chef or Puppet to install and configure the software within your Packer-made images.

