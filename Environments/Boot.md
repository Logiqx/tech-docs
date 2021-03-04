# Boot Configuration

Docker requires Hyper-V enabled whilst VirtualBox requires Hyper-V disabled.

In the past, I created multiple boot configurations so that I could switch between VirtualBox and Docker.

Nowadays, I just run with Hyper-V enabled and rarely use VirtualBox.



## Enable / Disable Hyper-V

To create a boot option with Hyper-V disabled:

```sh
C:\>bcdedit /copy {current} /d "No Hyper-V" 
The entry was successfully copied to {ff-23-113-824e-5c5144ea}. 

C:\>bcdedit /set {ff-23-113-824e-5c5144ea} hypervisorlaunchtype off 
The operation completed successfully.
```

To create a boot option with Hyper-V enabled:

```sh
C:\>bcdedit /copy {current} /d "Hyper-V" 
The entry was successfully copied to {ff-23-113-824e-5c5144ea}. 

C:\>bcdedit /set {ff-23-113-824e-5c5144ea} hypervisorlaunchtype auto 
The operation completed successfully.
```

### Reference

Scott Hanselman - [Switch easily between VirtualBox and Hyper-V with a BCDEdit boot Entry in Windows 8.1](http://www.hanselman.com/blog/SwitchEasilyBetweenVirtualBoxAndHyperVWithABCDEditBootEntryInWindows81.aspx)



## Check Boot Time

To check the last boot time:

```sh
C:\> systeminfo | find /i "Boot Time"
System Boot Time:          08/03/2019, 20:00:57

C:\> wmic os get lastbootuptime
LastBootUpTime
20190308200057.500000+000
```

### Reference

StackExchange - [How can I find out when Windows was last restarted?](https://superuser.com/questions/523726/how-can-i-find-out-when-windows-was-last-restarted)
