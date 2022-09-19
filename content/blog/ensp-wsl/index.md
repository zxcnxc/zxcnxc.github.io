+++
title = "eNSP VS WSL 2"
date = "2021-12-13"
description = "When you want to try Huawei while doing something in WSL"
tags = [
    "eNSP",
    "WSL",
    "Huawei",
    "Troubleshoot"
]
+++

---

{{< toc >}}

---


## TLDR 
Conflicts between Hypervisor.

## Issues
I wanted to learn more about Huawei devices, so i chose the Huawei Enterprise Network Simulator Platform [eNSP](https://support.huawei.com/enterprise/en/data-communication/ensp-pid-9017384?category=installation-upgrade) to simulate an enterprise network. <br>
So I followed the tutorial from a blog on the internet, but when I tried to run the program, I got the error message "Failed to start device AR1 error code 40." in eNSP.
<br>

{{< bundle-image name="huawei.png" alt="Error 40" caption="Failed to start device AR1" >}}

Then I searched for the error code message and found that WSL is incompatible with Virtualbox because WSL uses Virtual Platform and eNSP requires VirtualBox to run the simulation. [1](https://superuser.com/questions/1208850/why-cant-virtualbox-or-vmware-run-with-hyper-v-enabled-on-windows-10) <br>

Why not use the latest version of VirtualBox that supports Hyper-V?<br>

Unfortunately, until now, there has been no update from Huawei for eNSP. So if we try to install the latest VirtualBox and then run eNSP, an error message "VirtualBox version is not supported" will appear.

{{< bundle-image name="errorhuawei.png" alt="Error Not Support" caption="Virtualbox version not supported" >}}


## Setup
```HTML
OS Version : Windows 10 Pro 64bit Build 19044
Virtuabox Version : 5.2.44 r139111 (Qt5.6.2) 
WSL Version : 2
```
## Troubleshoot

In my case, I only need to disable Virtual Machine Platform

Therefore, the temporary solution is to disable Virtual Machine Platform / Hyper-V using 2 methods.

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

Hopefully, Huawei will release updates for eNSP to make it easier for WSL users, including students who want to learn Huawei devices and take the HCIA exam in the future. 

