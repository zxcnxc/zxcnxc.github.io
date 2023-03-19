+++
title = "Two Links to Different ISPs - Primary and Backup"
draft = true
date = "2023-01-01"
description = "When you want to try Huawei while doing something in WSL"
tags = [
    "eNSP",
    "WSL",
    "Huawei",
    "Troubleshoot"
]
+++

buildDrafts = true
---

{{< toc >}}

---


## TLDR 
Conflicts between Hypervisor.

## Issues
To learn more about Huawei devices, I decided to use the Huawei Enterprise Network Simulator Platform [eNSP](https://support.huawei.com/enterprise/en/data-communication/ensp-pid-9017384?category=installation-upgrade) to simulate an enterprise network. <br>
So I followed the tutorial from an internet blog, but when I tried to run the program, I received the error message "Failed to start device AR1 error code 40." in eNSP.
<br>

{{< bundle-image name="huawei.png" alt="Error 40" caption=" Failed to start device AR1" >}}

Then I looked for the error code message and discovered that [WSL](https://superuser.com/questions/1208850/why-cant-virtualbox-or-vmware-run-with-hyper-v-enabled-on-windows-10) is incompatible with VirtualBox because WSL uses Virtual Platform and eNSP requires VirtualBox to run the simulation. <br>

Why not use the most recent version of VirtualBox that supports Hyper-V?

Unfortunately, there has been no update from Huawei for eNSP until now.<br>
So, if we try to install the most recent VirtualBox and then run eNSP, we will get the message "VirtualBox version is not supported." 

{{< bundle-image name="errorhuawei.png" alt="Error Not Support" caption="Virtualbox version not supported" >}}


## Setup
```HTML
OS Version : Windows 10 Pro 64bit Build 19044
Virtuabox Version : 5.2.44 r139111 (Qt5.6.2) 
WSL Version : 2
```
## Troubleshoot

In my case, all I need to do is turn off Virtual Machine Platform.

As a result, the short-term solution is to disable Virtual Machine Platform / Hyper-V using one of two methods : 

1. Using Powershell <br>
To disable Hyper-V by using Windows PowerShell, follow these steps: 
    * Open an elevated PowerShell window.
    * Run the following command:
    ```powershell
    Disable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V-Hypervisor
    ```
    * Reboot
    
2. Using Windows Features
To disable Virtual Machine Platform in Control Panel, follow these steps: 
    * In Control Panel, select Programs and Features.
    * Select Turn Windows features on or off.
    * Uncheck the Virtual Machine Platform.

    {{< bundle-image name="hyper.png" alt="Hyper-V" caption="Uncheck Virtual Machine" >}}

    * Reboot
    
## Summary

Hopefully, Huawei will release eNSP updates to make it easier for WSL users, including students who want to learn Huawei devices and take the HCIA exam in the future. 

