+++
title = "Writeup LKSN ITNSA NTB 2021 - Linux Actual"
date = "2022-09-13"
description = "Configuration of Linux servers and clients"
canonical = "https://zxcnxc.github.io/writeup-itnsa-2021-linux-actual/"
tags = [
    "Linux",
    "ITNSA",
    "NTB",
    "Debian",
    "DNS",
    "NFS",
    "NGINX",
    "CRON",
]

draft = true

+++

---
<a name="top"></a>

{{< toc >}}

--- 

# REWRITE
- Bagian IP Configuration | Menambahkan gateway di sisi server254
- Hostname menggunakan /etc/resolve


## Introduction
Hello, I'm back in the final writeup of the third of three questions titled "Linux Actual," which is part of ITNSA's NTB LKSN competition in 2021.<br>

We are now asked to configure servers and clients using Linux Debian 10.10.0, installed as a Virtual Machine (VM) on VMWare Workstation.<br>

The following skills are required to solve this problem:

* Configuration of the hostname and IP address.
* Group and User Management
* Configuration of the Secure Shell (SSH).
* Configuration of the Web Server.
* Configuring the Domain Name System (DNS) with BIND.
* Mail Server Configuration (Postfix, Dovecot) and Web-based Email Roundcube
* Configuration of a File Server based on the Network File System (NFS).
* Setting up Scheduled Backup. 

Notes: I added 1 network adapter (NAT) for internet access to make installing the required packages easier.

For those who want to try this question, click on this [ITNSA](https://drive.google.com/file/d/1UELbE1HIXCeQ3IiSQ91fRXS79pRX3E-E/view).

{{< bundle-image name="TOPOLOGY-ITNSA-MODULE-C.PNG" alt="TOPOLOGY" caption="Topology ITNSA Module C" >}}

---

## TASK 1
### CONFIGURATION ON DEBIAN 10 SERVER (server1)

1. Install Debian 10 as a virtual machine (VM) with a 25 GB disk, 3 GB of RAM, and a single network adapter in Host Only mode. (Point: 15)
The following are the basic configuration requirements for Debian 10:
    * Hostname: server1 
    * Domain: mandalika.com 
    * Root Password: qwerty2021  
    * Regular user full name: System Administrator
    * Regular username: sysadmin
    * Regular user password: qwerty2021
    * Partition: using the entire disk with LVM settings
    * IP Address: 10.11.12.1/24
    * Software Selection: Debian desktop environment.

    {{< bundle-image name="SERVER1.PNG" alt="SERVER1">}}
    {{< bundle-image name="RootPass-Server1.PNG" alt="Root PW Server1">}}
    {{< bundle-image name="RegularFullName-Server1.PNG" alt="Fullname Server1">}}
    {{< bundle-image name="Username-Server1.PNG" alt="Username Server1">}}
    {{< bundle-image name="UserPass-Server1.PNG" alt="User PW Server1">}}
    {{< bundle-image name="PartitionLVM-Server1.PNG" alt="Partition LVM Server1">}}
    {{< bundle-image name="Software-Server1.PNG" alt="Software Server1">}}


Note: For the IP address, hostname and domain section, we will configure manually using the "nano" file editor with the location of the files in "/etc/network/interfaces", "/etc/resolv.conf", and /etc/hostname".

2. Configure sudo access for user "sysadmin". (Point: 2.5)

```HTML
sysadmin@server1:~$ su -
Password:
root@server1:~# usermod -aG sudo sysadmin
root@server1:~# exit
logout
sysadmin@server1:~$
```
Note: After this, sudo still won't work! You will need to logout from that user, then relogin, and sudo will work.

3. Create an alias interface so that server1 has a second IP address with the following ip address: 10.11.12.2/24. (Point: 5) <br>
Use ping to confirm the IP address or interface alias. 

```HTML
sysadmin@server1:~$ sudo nano /etc/network/interfaces

auto ens33
iface ens33 inet static
    address 10.11.12.1
    netmask 255.255.255.0

auto ens33:0
iface ens33:0 inet static
    address 10.11.12.2
    netmask 255.255.255.0
```
 {{< bundle-image name="PingAlias-Server1.PNG" alt="Ping to Alias Interface">}}


4. Install and configure a Domain Name System (DNS) server for the mandalika.com domain with the following requirements: (Point: 20)

    * The forward lookup zone file is named db.mandalika.
    * Create a hostname to IP address mapping for the second Debian 10 Linux VM with the name server254 and an IP address of 10.11.12.254.
    * Create an alias feature for www, kuta, and seger subdomains using CNAME.
    * The reverse lookup zone file's name is db.10. 

```HTML
sysadmin@server1:~$ sudo apt install bind9
sysadmin@server1:~$ cd /etc/bind
sysadmin@server1:/etc/bind$ sudo cp db.local db.mandalika
sysadmin@server1:/etc/bind$ sudo cp db.127 db.10
```

```HTML
sysadmin@server1:/etc/bind$ sudo nano db.mandalika    
```

{{< bundle-image name="ForwardZone-DB-Mandalika.PNG" alt="Forward Zone db.mandalika">}}

```HTML
sysadmin@server1:/etc/bind$ sudo nano db.10  
```

{{< bundle-image name="ReverseZone-DB-Mandalika.PNG" alt="Reverse Zone db.10">}}

Ensure that the mapping of domain names to IP addresses and vice versa is successful. 

{{< bundle-image name="NSLOOKUP-Server1.PNG" alt="NSLOOKUP Mandalika Server1">}}


5. Configure the DNS server for the rinjani.com domain with the following requirements: (Point: 15)
    * The forward lookup zone file name is db.rinjani.
    * Create an alias feature for www and gunung subdomains using CNAME.
    * Complete the reverse lookup zone settings in the db.10.

```HTML
sysadmin@server1:/etc/bind$ sudo cp db.domain db.rinjani
sysadmin@server1:/etc/bind$ sudo nano db.rinjani
```
{{< bundle-image name="ForwardZone-DB-Rinjani.PNG" alt="Forward Zone db.rinjani">}}
{{< bundle-image name="ReverseZone-DB-Ranjani.PNG" alt="Reverse Zone db.10">}}

Ensure that the mapping of domain names to IP addresses and vice versa is successful. 

{{< bundle-image name="NSLOOKUP-Rinjani-Server1.PNG" alt="NSLOOKUP Rinjani Server1">}}

6. Installed and configured a Network File System (NFS) server with the shared directory /LKS and full access permissions so that users from host server254 with IP address 10.11.12.254/24 can create and modify the directory's content. (Point: 15)

   Check that the directory /LKS is shared. 

```HTML
sysadmin@server1:~$ sudo apt install nfs-kernel-server
sysadmin@server1:~$ mkdir -p /LKS
sysadmin@server1:/$ sudo chown nobody:nogroup /LKS
sysadmin@server1:/$ sudo chmod 755 /LKS
sysadmin@server1:/$ sudo nano /etc/exports 
```
{{< bundle-image name="NTFS-Server1.PNG" alt="NTFS Server Setting">}}

```HTML
sysadmin@server1:/$ sudo exportfs -a
sysadmin@server1:/$ systemctl restart nfs-server
```

7. Configure the public_html directory to be created automatically in the home directory of each newly created user. (Point: 5)

```HTML
sysadmin@server1:~$ sudo mkdir -p /etc/skel/public_html
```

{{< bundle-image name="Mkdir-Server1.PNG" alt="MKDIR public_html automatically">}}


8. Create users with the names "mandalika," "kuta," "seger," "rinjani," and "gunung" and the password "qwerty2021". (Point: 30)

    Also, ensure that when the user is created, the home directory uses /wwwroot. For example, /wwwroot/mandalika for user "mandalika." 

```HTML
sysadmin@server1:~$ sudo nano /etc/adduser.conf 
```
{{< bundle-image name="AddUser-Server1.PNG" alt="Change default home location to /wwwroot">}}

```HTML
sysadmin@server1:~$ sudo adduser mandalika 
sysadmin@server1:~$ sudo adduser kuta
sysadmin@server1:~$ sudo adduser seger
sysadmin@server1:~$ sudo adduser rinjani
sysadmin@server1:~$ sudo adduser gunung
```
{{< bundle-image name="AddUser1-Server1.PNG" alt="Change default home location to /wwwroot">}}
{{< bundle-image name="AddUser2-Server1.PNG" alt="Change default home location to /wwwroot">}}


9. Create an index.html file with the content "Welcome to user Homepage" in the public_html directory of each user's home directory. (Point: 12.5) <br>

    Replace user with each user's login name. 

```HTML
sysadmin@server1:/wwwroot$ sudo find -name public_html -exec touch '{}/index.html' \;
sysadmin@server1:/wwwroot$ tree
```
{{< bundle-image name="CreateIndexHTML-Server1.PNG" alt="Change default home location to /wwwroot">}}

```HTML
sysadmin@server1:/wwwroot$ sudo su
root@server1:/wwwroot# sudo echo -e 'Welcome to Gunung Homepage' >> gunung/public_html/index.html
root@server1:/wwwroot# sudo echo -e 'Welcome to Kuta Homepage' >> kuta/public_html/index.html
root@server1:/wwwroot# sudo echo -e 'Welcome to Mandalika Homepage' >> mandalika/public_html/index.html
root@server1:/wwwroot# sudo echo -e 'Welcome to Rinjani Homepage' >> rinjani/public_html/index.html
root@server1:/wwwroot# sudo echo -e 'Welcome to Seger Homepage' >> seger/public_html/index.html
```

```HTML
sysadmin@server1:/wwwroot$ sudo find -name public_html -exec cat '{}/index.html' \;
```

{{< bundle-image name="ContentIndexHTML-Server1.PNG" alt="Content on all index.html">}}
    

10. Install the NGINX web server and configure the Virtual Host with the following conditions:
(Point: 25)

     Virtual Host Domain               | Key
     ------                            | -----
     www.mandalika.com (mandalika.com) | /wwwroot/mandalika/public_html
     kuta.mandalika.com                | /wwwroot/kuta/public_html
     seger.mandalika.com               | /wwwroot/seger/public_html
     www.rinjani.com (rinjani.com)     | /wwwroot/rinjani/public_html
     gunung.rinjani.com                | /wwwroot/gunung/public_html


```HTML
sysadmin@server1:~$ sudo apt install nginx
sysadmin@server1:/wwwroot$ sudo chown -R www-data: /wwwroot/
sysadmin@server1:/wwwroot$ sudo chown -R www-data: /wwwroot/kuta/
sysadmin@server1:/wwwroot$ sudo chown -R www-data: /wwwroot/gunung/
sysadmin@server1:/wwwroot$ sudo chown -R www-data: /wwwroot/mandalika/
sysadmin@server1:/wwwroot$ sudo chown -R www-data: /wwwroot/seger/
sysadmin@server1:/wwwroot$ sudo chown -R www-data: /wwwroot/rinjani/
```

```HTML
sysadmin@server1:/wwwroot$ sudo nano /etc/nginx/sites-available/mandalika.com.conf
```
{{< bundle-image name="MandalikaConf-Server1.PNG" alt="Mandalika Config">}}

```HTML
sysadmin@server1:/wwwroot$ sudo nano /etc/nginx/sites-available/kuta.mandalika.com.conf
```
{{< bundle-image name="KutaMandalikaConf-Server1.PNG" alt="Kuta Mandalika Config">}}

```HTML
sysadmin@server1:/wwwroot$ sudo nano /etc/nginx/sites-available/seger.mandalika.com.conf
```
{{< bundle-image name="SegerMandalikaConf-Server1.PNG" alt="Seger Mandalika Config">}}

```HTML
sysadmin@server1:/wwwroot$ sudo nano /etc/nginx/sites-available/rinjani.com.conf
```
{{< bundle-image name="RinjaniConf-Server1.PNG" alt="Rinjani Config">}}

```HTML
sysadmin@server1:/wwwroot$ sudo nano /etc/nginx/sites-available/gunung.rinjani.com.conf
````
{{< bundle-image name="GunungRinjaniConf-Server1.PNG" alt="Gunung Rinjani Config">}}

```HTML
sysadmin@server1:/wwwroot$ sudo ln -s /etc/nginx/sites-available/* /etc/nginx/sites-enabled/
```

```HTML
sysadmin@server1:/wwwroot$ sudo nginx -t
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
sysadmin@server1:/wwwroot$ sudo systemctl restart nginx
```

{{< bundle-image name="CaptureWebsiteNginx-Server1.PNG" alt="Virtualhost Access Successfull">}}


11. Configure user "sysadmin" on server1 to be able to perform SSH access to server2 without authentication. (Point: 15)  <br>

12. Create a shell script that backups all content from the server1 host's /wwwroot directory to server254 in the /backup-wwwroots directory on a regular basis, i.e. every 5 minutes. (Point: 25)

    The backup file is compressed and named backup date-month-year-hour-second.tar.gz<br>
    For example, backup 01-09-2021 09-15-00.tar.gz is the backup file name for a backup performed on September 1 at 09:15:00.<br>
    On server1, ensure that the shell script is run by the user "sysadmin"

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
    sysadmin@server254:~$ su -
    Password:
    root@server254:~# usermod -aG sudo sysadmin
    root@server254:~# exit
    logout
    sysadmin@server254:~$
    ```


3. Create a new user with the name "webmaster" with the password "qwerty2021". (Point: 5)

    ```HTML
    ```

4. Install and configure the email server using Postfix + Dovecot and web based email using Roundcube. (Point: 20) <br>
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

    ```HTML
    ```

7. Verify the success of the scheduled backup by looking at the contents of the /backup-wwwroot directory. (Point: 5) <br>
Make sure there is a backup file with the naming format backup_date-month-year_hour-minute-second.tar.gz. 

    ```HTML
    ```

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