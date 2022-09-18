+++
title = "Writeup ITNSA 2021 - Linux Actual"
date = "2022-09-13"
description = "Writeup for ITNSA 2021 Module C : LINUX ACTUAL"
tags = [
    "Linux",
    "ITNSA",
    "Debian",
    "DNS",
    "NFS",
    "NGINX",
    "CRON",
]

draft = true

+++

---

{{< toc >}}

--- 

## Introduction
<a id="top"></a>

Hello, meet again in the last question in ITNSA 2021, in this module we will try to install a Debian 10 Server and configure DNS, NFS, NGINX, Roundcube for email, scheduled backups using CRON and Verify access to domains that have been created. <br>

Note, I added 1 more network adapter, namely NAT for access to the internet to make it easier to install the package needed. <br>

For those interested in trying it can be downloaded from this link [ITNSA](https://drive.google.com/file/d/1UELbE1HIXCeQ3IiSQ91fRXS79pRX3E-E/view)

![Topology](/images/ITNSA-MODULE-C/TOPOLOGY.PNG)

---

## TASK 1
### CONFIGURATION ON DEBIAN 10 SERVER (server1) <br>
1. Install Debian 10 as a VM with 25 GB disk capacity, 3 GB RAM and 1 network adapter with Host Only mode. (Point: 15) <br>
Basic configuration requirements on Debian 10 are as follows:  <br>
    * Hostname: server1 <br>
    * Domain: mandalika.com <br>
    * Root Password: qwerty2021 <br> 
    * Regular user full name: System Administrator
    * Regular username: sysadmin
    * Regular user password: qwerty2021
    * Partition: using the entire disk with LVM settings
    * IP Address: 10.11.12.1/24
    * Software Selection: Debian desktop environment.

![INITIAL-SERVER1](/images/ITNSA-MODULE-C/SERVER1.PNG)



2. Configure sudo access for user "sysadmin". (Point: 2.5)

```HTML
```

3. Create an alias interface so that server1 has a 2nd IP address with a value of 10.11.12.2/24. (Point: 5) <br>
Verify the results of creating the alias interface using the ping utility. 

```HTML
```

4. Install and configure a Domain Name System (DNS) server for the mandalika.com domain with the following requirements: (Point: 20)
    * The name of the forward lookup zone file is db.mandalika.
    * Create a hostname to IP address mapping for the second Debian 10 Linux VM with the name server254 which has an IP address of 10.11.12.254.
    * Create an alias feature for subdomains using CNAME for www, kuta and seger.
    * The name of the reverse lookup zone file is db.10. <br>

Ensure that the mapping of domain names to IP addresses and vice versa is successful.

```HTML
```

5. Configure the DNS server for the rinjani.com domain with the following requirements: (Point: 15)
    * The forward lookup zone file name is db.rinjani.
    * Create an alias feature for subdomains using CNAME for www and gunung.
    * Complete the reverse lookup zone settings in the db.10 file. <br>

Confirm that the mapping of domain name to IP address and vice versa is successful. 

```HTML
```

6. Installed and configured a Network File System (NFS) server with the provision that the shared directory is /LKS with full access permissions so that users from host server254 with IP address 10.11.12.254/24 can create and modify the content in the directory. (Point: 15) <br>
Verification that the directory /LKS is shared. 

```HTML
```

7. Configure the public_html directory to be automatically created in the home directory of each new user created. (Point: 5)

```HTML
```

8. Create users with the names "mandalika", "kuta", "seger", "rinjani" and "gunung" with the password "qwerty2021". (Point: 30) <br>
Also make sure that when the user is created, the home directory uses /wwwroot, for example for user "mandalika" then the home directory is /wwwroot/mandalika. 

```HTML
```

9. Create an index.html file in the public_html directory contained in each home directory of the user with the content "Welcome to user Homepage". (Point: 12.5)<br>
 Replace user with the login name of each user. 

```HTML
```


10. Install the NGINX web server and configure the Virtual Host with the following conditions 
as follows: (Point: 25).

     Virtual Host Domain               | Key
     ------                            | -----
     www.mandalika.com (mandalika.com) | /wwwroot/mandalika/public_html
     kuta.mandalika.com                | /wwwroot/kuta/public_html
     seger.mandalika.com               | /wwwroot/seger/public_html
     www.rinjani.com (rinjani.com)     | /wwwroot/rinjani/public_html
     gunung.rinjani.com                | /wwwroot/gunung/public_html



11. Configure that user "sysadmin" on server1 can perform SSH access using user "sysadmin" to server2 without authentication (Point: 15). <br>

12. Create a shell script that functions to backup all content from the /wwwroot directory on the server1 host to server254 in the /backup-wwwroots directory on a scheduled basis, namely every 5 minutes. (Point: 25) <br>
The backup file is in a compressed format with the naming format backup_tanggal-month-year-hour-second.tar.gz.  <br>
For example, for a backup performed on September 1 at 09:15:00, the backup file name is backup_01-09-2021_09-15-00.tar.gz. <br>
Make sure the shell script is executed by user "sysadmin" on server1. <br>

--- 




## TASK 2
### CONFIGURATION ON DEBIAN 10 SERVER (server254) <br>
1. Install Debian 10 as a VM with 25 GB disk capacity, 3 GB RAM and 1 network adapter with Host Only mode. (Point: 15) <br>
Basic configuration requirements on Debian 10 are as follows:  <br>
    * Hostname: server254 <br>
    * Domain: mandalika.com <br>
    * Root Password: qwerty2021 <br> 
    * Regular user full name: System Administrator
    * Regular username: sysadmin
    * Regular user password: qwerty2021
    * Partition: using the entire disk with LVM settings
    * IP Address: 10.11.12.254/24
    * Software Selection: Debian desktop environment.

```HTML
```

2. Configure sudo access for user "sysadmin". (Point: 2.5)

```HTML
```


3. Create a new user with the name "webmaster" with the password "qwerty2021". (Point: 5)

```HTML
```

4. Install and configure the email server using Postfix + Dovecot and web based email using Roundcube. (Point: 20) <br  >
Test sending an email from user "sysadmin" to "webmaster" and vice versa. <br>
Make sure the email is sent successfully. 

```HTML
```

5. Install the Network File System (NFS) Client and configure it to be able to access the shared NFS directory on the host server1 in the directory /data-LKS directory permanently. (Point: 15) <br>
Make sure that when hostserver254 is rebooted it can directly access the shared NFS directory automatically. <br>
Verify by reboot server2 and make sure the NFS shared directory has been accessed permanently.  <br>

```HTML
```

6. Create a new file with the name "smkbisa.txt" with the content "ITNSA LKS SMK NTB 2021" in the /data-LKS directory. (Point: 5) <br>
Make sure the file is successfully created.  

7. Verify the success of the scheduled backup by looking at the contents of the /backup-wwwroot directory. (Point: 5) <br>
Make sure there is a backup file with the naming format backup_date-month-year_hour-minute-second.tar.gz. 

8. Verify access via browser to each of the following domains: (Point: 12.5) <br>

    * www.mandalika.com (mandalika.com) <br>
    * kuta.mandalika.com <br>
    * seger.mandalika.com <br>
    * www.rinjani.com (rinjani.com) <br>
    * gunung.rinjani.com <br>

---

## Summary

The questions are not too difficult, but I found difficulty in the part of question no. 1 which has a bug (?).<br>
Sometimes in BRANCH1 the error "ISAKMP (0:1): Can not start Aggressive mode, trying Main mode."<br>
And at HQ the error "%CRYPTO-4-RECVD_PKT_INV_SPI: decaps: rec'd IPSEC packet has invalid spi for" appears. <br>

It happens if I reload a .PKT file, so I have to try reloading several times to avoid getting that bug.

Maybe this post will be updated if I have found a solution for these 2 bugs. <br>
See you again in the next post