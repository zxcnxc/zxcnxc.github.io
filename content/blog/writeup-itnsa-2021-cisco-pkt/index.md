+++
title = "Writeup ITNSA 2021 - Network Actual & Future Network"
date = "2022-03-15"
description = "Build a corporate network from the ground up using Cisco devices."
tags = [
    "Cisco",
    "ITNSA",
    "Packet Tracer",
    "GRE OVER IPSEC",
    "OSPF",
    "DHCP",
    "NAT",
    "NTP",
    "RBAC",
    "WLC",
    "LWAP",
    "InterVLAN",
    "RADIUS"
]
+++

---
<a name="top"></a>

{{< toc >}}

--- 

# Introduction
So I was bored on the internet, looking for information about CCNA lab questions, when I came upon the 2021 LKS (Student Competency Competition) questions published by ITNSA.

The lab is divided into three categories: 
1. Network Actual & Future Network
2. Troubleshooting Cisco Packet Tracer
3. Actual Linux

This lab has 20 problems that require us to configure the Server, Router, Wireless Router, and Client Verification. 

This lab is done using [Cisco Packet Tracer version 8.1](https://www.netacad.com/courses/packet-tracer).<br>
The lab download link is available [ITNSA](https://drive.google.com/file/d/1pFU9v1WX_XH-4FkNXJH-pzzBErqbfBaN/view?usp=sharing).

{{< bundle-image name="TOPOLOGY-ITNSA.PNG" alt="TOPOLOGY" caption="Topology ITNSA Module A" >}}

---

## Task 1
### HQ MATARAM Server Configuration

1. Set the IP addressing and default gateway and DNS on the HQ Server with the following conditions: 
   
    *  IP Address: the second IP address of subnet 10.0.14.0/30.

    {{< bundle-image name="IP-Subnet-HQ-Mataram.PNG" alt="IP Address at HQ MATARAM">}}

    *  Default Gateway: the first IP address of subnet 10.0.14.0/30.
    *  DNS Server: 192.0.2.1

    {{< bundle-image name="Gateway-DNS-HQ-Mataram.PNG" alt="Gateway and DNS at HQ MATARAM">}}

2. Enable HTTP and HTTPS services.

    {{< bundle-image name="HTTP-HTTPS-HQ-MATARAM.PNG" alt="Enable HTTP and HTTPS services">}}

3. Enable AAA service and configure Network Configuration with Server Radius type and User Setup as given in the table below: 

    Client Name  | Client IP                            | Key
    --------     | ------                               | -----
    WLC_HQ       | 10.0.13.0.4                          | SMKHebat2021
    HQ           | 10.0.14.1                            | NTBJawara2021 

    <br>

    Username        | Password
    --------        |------
    hrd1            | hrd1
    hrd2            | hrd2
    mkt1            | mkt1
    mkt2            | mkt2
    adminHQ         | LksNTB2021
    supportHQ       | mandalika

    {{< bundle-image name="AAA-HQ-Mataram.PNG" alt="Enable AAA service">}}

### Completed Points: 8%

<a href="#top">Back to top</a>

---

## Task 2
### HQ MATARAM Router Configuration

1. Set the router's hostname with the name "HQ". 

```HTML
Router>en
Router#conf t
Enter configuration commands, one per line. End with CNTL/Z.
Router(config)#hostname HQ
```

2. Set the encapsulation protocol on the Serial0/0/0 interface connected to the ISP router through PPP and use PPP authentication using CHAP with the password "LksNTB".  

```HTML
HQ(config)#int s0/0/0
HQ(config-if)#encapsulation ppp
HQ(config-if)#ppp authentication chap 
HQ(config)#username ISP password LksNTB
```

3. Configure the IP addressing on the Serial0/0/0 interface connected to the ISP router with the second IP address from subnet 192.0.2.16/29.

```HTML
HQ(config)#int s0/0/0
HQ(config-if)#ip address 192.0.2.18 255.255.255.248
HQ(config-if)#no shut
%LINK-5-CHANGED: Interface Serial0/0/0, changed state to up
%LINEPROTO-5-UPDOWN: Line protocol on Interface Serial0/0/0, changed state to up
HQ(config-if)#description TO ISP
```

4. Configure the IP addressing on the GigabitEthernet0/1 interface connected to the HQ Server with the first IP address from subnet 10.0.14.0/30. 

```HTML
HQ(config)#int g0/1
HQ(config-if)#ip address 10.0.14.1 255.255.255.252
HQ(config-if)#no shut
%LINK-5-CHANGED: Interface GigabitEthernet0/1, changed state to up
%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/1, changed state to up
HQ(config-if)#description TO HQ Server
```

5. Create a subinterface on the GigabitEthernet0/0 interface that implements the IEEE 802.1Q encapsulation protocol and configure the router-on-stick for VLAN communication. <br>
The following conditions are used to assign IP addresses to each subinterface:<br> 
    
    * Subinterface GigabitEthernet0/0.11 for VLAN 11 HRD with the first IP address of the subnet address 10.0.11.0/24.
    * Subinterface GigabitEthernet0/0.12 for VLAN 12 MARKETING with the first IP address of the subnet address 10.0.12.0/24.
    * Subinterface GigabitEthernet0/0.13 for VLAN 13 NATIVE is configured as a native VLAN and utilizes the first IP address in the subnet address 10.0.13.0/24. 
            
```HTML            
HQ(config)#int g0/0
HQ(config-if)#description ROUTER-ON-STICK
%LINK-5-CHANGED: Interface GigabitEthernet0/0, changed state to up
HQ(config-if)#no shut
%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0, changed state to up

HQ(config-if)#int g0/0.11
%LINK-5-CHANGED: Interface GigabitEthernet0/0.11, changed state to up
%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0.11, changed state to up
HQ(config-subif)#description VLAN 11 HRD
HQ(config-subif)#encapsulation dot1Q 11 
HQ(config-subif)#ip address 10.0.11.1 255.255.255.0

HQ(config)#int g0/0.12
%LINK-5-CHANGED: Interface GigabitEthernet0/0.12, changed state to up
%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0.12, changed state to up
HQ(config-subif)#description VLAN 12 MARKETING
HQ(config-subif)#encapsulation dot1Q 12
HQ(config-subif)#ip address 10.0.12.1 255.255.255.0

HQ(config)#int g0/0.13
%LINK-5-CHANGED: Interface GigabitEthernet0/0.13, changed state to up
%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0.13, changed state to up
HQ(config-subif)#description VLAN 13 NATIVE
HQ(config-subif)#encapsulation dot1Q 13 native 
HQ(config-subif)#ip address 10.0.13.1 255.255.255.0
```            

6. Creating a DHCP Server:
    * Pool name "HRD" for VLAN 11 with subnet 10.0.11.0/24.
    * Pool name "MARKETING" for VLAN 12 with subnet 10.0.12.0/24.
    * Pool name "NATIVE" for VLAN 13 with subnet 10.0.13.0/24.

    The TCP/IP parameters configured on each pool are as follows:
    * The DHCP Client obtains the default gateway by using the first IP address of each VLAN.
    * DNS for the entire pool utilizes the DNS ROOT Server's IP address, which is 192.0.2.1.
    * The IP address distribution of the Wireless Controller WLC HQ is also carried out for the "NATIVE" pool, specifically using the fourth IP address of the 10.0.13.0/24 subnet, so that the LeighWeight Access Point (LWAP) can register automatically. 

```HTML
HQ(config)#ip dhcp pool HRD
HQ(dhcp-config)#network 10.0.11.0 255.255.255.0
HQ(dhcp-config)#default-router 10.0.11.1
HQ(dhcp-config)#dns-server 192.0.2.1

HQ(config)#ip dhcp pool MARKETING
HQ(dhcp-config)#network 10.0.12.0 255.255.255.0
HQ(dhcp-config)#default-router 10.0.12.1
HQ(dhcp-config)#dns-server 192.0.2.1

HQ(config)#ip dhcp pool NATIVE
HQ(dhcp-config)#network 10.0.13.0 255.255.255.0
HQ(dhcp-config)#default-router 10.0.13.1
HQ(dhcp-config)#dns-server 192.0.2.1
```

For each pool, specify the IP address that is not leased to the DHCP Client:

   * The initial IP address of the VLAN 11 HRD and VLAN 12 MARKETING subnet addresses.
   * The first through fourth IP addresses of VLAN 13 NATIVE's subnet addresses.

```HTML        
HQ(config)#ip dhcp excluded-address 10.0.11.1
HQ(config)#ip dhcp excluded-address 10.0.12.1
HQ(config)#ip dhcp excluded-address 10.0.13.1 10.0.13.4
```

7. Set the default route to the ISP using the gateway of the first IP address in the subnet 192.0.2.16/29 used by the ISP router's Serial0/0/0 interface.

```HTML    
HQ(config)#ip route 0.0.0.0 0.0.0.0 192.0.2.17
```

8. Create an Extended Named ACL with the name "ALLOW-INTERNET" and the following conditions: 

    * Allow any source to send queries to the DNS server with the Public IP address 192.0.2.1 to translate domain names to IP addresses and vice versa.  
    * Allow Internet access ONLY to the gmail.com email service with a Public IP address of 192.0.2.5 for all hosts in VLAN 11 HRD with a subnet address of 10.0.11.0/24. <br>
    Ensure that only emails from gmail.com can be sent and received by the hosts on that VLAN.
    * Allow Internet connectivity to all hosts on VLAN 12 MARKETING with subnet address 10.0.12.0/24 and VLAN 13 NATIVE with subnet address 10.0.13.0/24.  

```HTML
HQ(config)#ip access-list extended ALLOW-INTERNET
HQ(config-ext-nacl)#permit udp any host 192.0.2.1 eq 53 (Query DNS) 
HQ(config-ext-nacl)#permit tcp 10.0.11.0 0.0.0.255 192.0.2.5 0.0.0.0 eq 25  (Email)
HQ(config-ext-nacl)#permit tcp 10.0.11.0 0.0.0.255 192.0.2.5 0.0.0.0 eq 110 (Email) 
HQ(config-ext-nacl)#permit tcp 10.0.12.0 0.0.0.255 0.0.0.0 255.255.255.255  (Internet)
HQ(config-ext-nacl)#permit tcp 10.0.13.0 0.0.0.255 0.0.0.0 255.255.255.255  (Internet)
```            

9. Set NAT Overload or Port Address Translation (PAT) for Standard Named ACL "ALLOW-INTERNET".

```HTML
HQ(config)#int s0/0/0
HQ(config-if)#ip nat outside
HQ(config)#int ra g0/0.11 - g0/0.13
HQ(config-if-range)#ip nat inside
HQ(config)#ip nat inside source list ALLOW-INTERNET interface s0/0/0 overload
```

10. Set static NAT so that ONLY HTTP and HTTPS services on the HQ server with Private IP address 10.0.14.2 are translated to Public IP address 192.0.2.19.

```HTML
HQ(config)#ip nat inside source static 10.0.14.2 192.0.2.19
HQ(config)#int g0/1
HQ(config-if-range)#ip nat inside
```

11. Set up the HQ router to synchronize time with the NTP Server at IP address 192.0.2.2 and use md5 authentication with key 46 and password "NTBHebat2021". 

```HTML
HQ(config)#ntp server 192.0.2.2 key 46
HQ(config)#ntp authentication-key 46 md5 NTBHebat2021
HQ(config)#ntp trusted-key 46
```

12. Set the privilege mode password with the secret "LksNTB2021". 

```HTML
HQ(config)#enable secret LksNTB2021
```

13. Set the DNS server of the HQ router with IP address 192.0.2.1.

```HTML
HQ(config)#ip name-server 192.0.2.1
```    
14. Enable SSH version 2 on the HQ router with the domain name "mandalika.id" and create RSA keys with modulus 1024. 

```HTML
HQ(config)#ip domain-name mandalika.id
HQ(config)#ip ssh version 2
HQ(config)#crypto key generate rsa general-keys modulus 1024
% You already have RSA keys defined named HQ.mandalika.id
% They will be replaced.

% The key modulus size is 1024 bits
% Generating 1024 bit RSA keys, keys will be non-exportable...[OK]
HQ(config)#
*Jul 22 14:10:45.255: %SSH-5-ENABLED: SSH 2 has been enabled
 ```

15. Create a local authentication account on the HQ router with username "admin" and password "LksNTB2021". <br>
This account is a backup when authentication to the Radius server has problems.  

```HTML        
HQ(config)#username admin privilege 15 secret LksNTB2021
HQ(config)#line vty 0 4
HQ(config-line)#login local
HQ(config-line)#transport input ssh
HQ(config)#line console 0
HQ(config-line)#login local
HQ(config)#line aux 0
HQ(config-line)#login local
```

16. Enabled the AAA feature on the HQ router and set the default authentication to use the "local" database.  

```HTML  
HQ(config)#aaa new-model
HQ(config)#aaa authentication login default local
```

17. Creating a user login authentication list with the name "SERVER-AAA" that uses the Radius authentication method first, followed by "local". 

```HTML    
HQ(config)#aaa authentication login SERVER-AAA group RADIUS local
```

18. Connect to the Radius Server with the second IP address of the subnet 10.0.14.0/30 and use the secret "NTBJawara2021". 

```HTML    
HQ(config)#RADIUS server SERVER-AAA 
HQ(config-RADIUS-server)#address ipv4 10.0.14.2 auth-port 1645
HQ(config-RADIUS-server)#key NTBJawara2021
```

19. Set the HQ router to only accept remote access via SSH with a maximum number of sessions for 5 (five) users simultaneously and using the authentication list "SERVER-AAA".  

```HTML       
HQ(config)#line vty 0 4
HQ(config-line)#login authentication SERVER-AAA
HQ(config-line)#session-limit 5
```

20. Set the console access to the router also to use the authentication list "SERVER-AAA" AUTHENTICATION LIST. 

```HTML    
HQ(config)#line con 0
HQ(config-line)#login authentication SERVER-AAA
```

21. Verify the configuration that has been done to ensure that it is following the requirements. 
    
```HTML
HQ#sh run
Building configuration...

Current configuration : 2932 bytes
!
version 15.1
no service timestamps log datetime msec
no service timestamps debug datetime msec
no service password-encryption
!
hostname HQ
!
enable secret 5 $1$mERr$ix/GaeUGI2eMcE7/bu9c2/
!
ip dhcp excluded-address 10.0.11.1
ip dhcp excluded-address 10.0.12.1
ip dhcp excluded-address 10.0.13.1 10.0.13.4
!
ip dhcp pool HRD
network 10.0.11.0 255.255.255.0
default-router 10.0.11.1
dns-server 192.0.2.1
ip dhcp pool MARKETING
network 10.0.12.0 255.255.255.0
default-router 10.0.12.1
dns-server 192.0.2.1
ip dhcp pool NATIVE
network 10.0.13.0 255.255.255.0
default-router 10.0.13.1
dns-server 192.0.2.1
!
aaa new-model
!
aaa authentication login SERVER-AAA group RADIUS local 
aaa authentication login default local 
!
no ip cef
no ipv6 cef
!
username ISP password 0 LksNTB
username admin privilege 15 secret 5 $1$mERr$ix/GaeUGI2eMcE7/bu9c2/
!
license udi pid CISCO1941/K9 sn FTX1524O1D2-
license boot module c1900 technology-package securityk9
!
ip ssh version 2
ip domain-name mandalika.id
ip name-server 192.0.2.1
!
spanning-tree mode pvst
!
interface GigabitEthernet0/0
description ROUTER-ON-STICK
no ip address
duplex auto
speed auto
!
interface GigabitEthernet0/0.11
description VLAN 11 HRD
encapsulation dot1Q 11
ip address 10.0.11.1 255.255.255.0
ip nat inside
!
interface GigabitEthernet0/0.12
description VLAN 12 MARKETING
encapsulation dot1Q 12
ip address 10.0.12.1 255.255.255.0
ip nat inside
!
interface GigabitEthernet0/0.13
description VLAN 13 NATIVE
encapsulation dot1Q 13 native
ip address 10.0.13.1 255.255.255.0
ip nat inside
!
interface GigabitEthernet0/1
description TO HQ SERVER
ip address 10.0.14.1 255.255.255.252
ip nat inside
duplex auto
speed auto
!
interface Serial0/0/0
description TO ISP
ip address 192.0.2.18 255.255.255.248
encapsulation ppp
ppp authentication chap
ip nat outside
!
interface Serial0/0/1
no ip address
clock rate 2000000
shutdown
!
interface Vlan1
no ip address
shutdown
!
ip nat inside source list ALLOW-INTERNET interface Serial0/0/0 overload
ip nat inside source static 10.0.14.2 192.0.2.19 
ip classless
ip route 0.0.0.0 0.0.0.0 192.0.2.17 
!
ip flow-export version 9
!
ip access-list extended ALLOW-INTERNET
permit udp any host 192.0.2.1 eq domain
permit tcp 10.0.11.0 0.0.0.255 host 192.0.2.5 eq smtp
permit tcp 10.0.11.0 0.0.0.255 host 192.0.2.5 eq pop3
permit tcp 10.0.12.0 0.0.0.255 any
permit tcp 10.0.13.0 0.0.0.255 any
!
no cdp run
!
radius server SERVER-AAA
address ipv4 10.0.14.2 auth-port 1645
key NTBJawara2021
radius server 10.0.14.2
address ipv4 10.0.14.2 auth-port 1645
key NTBJawara2021
!
line con 0
login authentication SERVER-AAA
!
line aux 0
!
line vty 0 4
session-limit 5
login authentication SERVER-AAA
transport input ssh
!
ntp authentication-key 46 md5 080F786C211C071606595C567B 7
ntp trusted-key 46
ntp server 192.0.2.2 key 46
!
end

HQ#
```

### Completed Points:  19%

<a href="#top">Back to top</a>

---

## Task 3
### SW_HQ_L1 Switch Configuration at HQ MATARAM

1. Set the hostname of the Switch with the name "SW_HQ_L1".

```HTML        
Switch>en
Switch#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Switch(config)#hostname SW_HQ_L1
```

2. Set the VTP with the domain name "MANDALIKA" and password using "LksNTB".

```HTML     
SW_HQ_L1(config)#vtp domain MANDALIKA
Changing VTP domain name from NULL to MANDALIKA
SW_HQ_L1(config)#vtp password LksNTB
Setting device VLAN database password to LksNTB
```

3. Creating new VLANs, among others: 
    * VLAN 11 with the name HRD. 
    * VLAN 12 with the name MARKETING. 
    * VLAN 13 with the name NATIVE. 

```HTML   
SW_HQ_L1(config)#vlan 11
SW_HQ_L1(config-vlan)#name HRD
SW_HQ_L1(config-vlan)#vlan 12
SW_HQ_L1(config-vlan)#name MARKETING
SW_HQ_L1(config-vlan)#vlan 13
SW_HQ_L1(config-vlan)#name NATIVE
SW_HQ_L1#sh vlan
VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
11   HRD                              active    
12   MARKETING                        active    
13   NATIVE                           active    
```

4. Set the IP addressing on the VLAN 13 interface with the second IP address of the subnet address 10.0.13.0/24.

```HTML
SW_HQ_L1(config)#int vlan 13
%LINK-5-CHANGED: Interface Vlan13, changed state to up
SW_HQ_L1(config-if)#ip address 10.0.13.2 255.255.255.0
```

5. Set the default gateway using the first IP address of the subnet address 10.0.13.0/24, one of the IP addresses on the HQ router, so that it can communicate with different networks.

```HTML    
SW_HQ_L1(config)#ip default-gateway 10.0.13.1
```

6. Assign port FastEthernet0/1 to be a member of VLAN 13. 

```HTML
SW_HQ_L1(config)#int fa0/1 
SW_HQ_L1(config-if)#switchport mode access 
SW_HQ_L1(config-if)#switchport access vlan 13
%LINEPROTO-5-UPDOWN: Line protocol on Interface Vlan13, changed state to up
```

7. Enable port mode to trunk for interfaces FastEthernet0/10, FastEthernet0/11, GigabitEthernet0/1 and GigabitEthernet0/2. 
<b>Notes</b>: VLAN 13 is enabled as a native VLAN.  

```HTML
SW_HQ_L1(config)#int ra fa0/10 - fa0/11
SW_HQ_L1(config-if-range)#switchport mode trunk 
SW_HQ_L1(config-if-range)#switchport trunk native vlan 13 
SW_HQ_L1(config-if-range)#switchport trunk allowed vlan 11,12,13

%LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/10, changed state to down
%LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/10, changed state to up
%LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/11, changed state to down
%LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/11, changed state to up

SW_HQ_L1(config)#int ra g0/1 - g0/2
SW_HQ_L1(config-if-range)#switchport mode trunk
SW_HQ_L1(config-if-range)#switchport trunk native vlan 13 
SW_HQ_L1(config-if-range)#switchport trunk allowed vlan 11,12,13

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/1, changed state to down
%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/1, changed state to up
%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/2, changed state to down
%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/2, changed state to up
```

8. Verify the configuration that has been done to ensure that it is following the requirements. 

```HTML
SW_HQ_L1#sh run
Building configuration...

Current configuration : 1591 bytes
!
version 15.0
no service timestamps log datetime msec
no service timestamps debug datetime msec
no service password-encryption
!
hostname SW_HQ_L1
!
spanning-tree mode pvst
spanning-tree extend system-id
!
interface FastEthernet0/1
switchport access vlan 13
switchport mode access
!
interface FastEthernet0/10
switchport trunk native vlan 13
switchport trunk allowed vlan 11-13
switchport mode trunk
!
interface FastEthernet0/11
switchport trunk native vlan 13
switchport trunk allowed vlan 11-13
switchport mode trunk
!
interface GigabitEthernet0/1
switchport trunk native vlan 13
switchport trunk allowed vlan 11-13
switchport mode trunk
!
interface GigabitEthernet0/2
switchport trunk native vlan 13
switchport trunk allowed vlan 11-13
switchport mode trunk
!
interface Vlan1
no ip address
shutdown
!
interface Vlan13
ip address 10.0.13.2 255.255.255.0
!
ip default-gateway 10.0.13.1
!
line con 0
!
line vty 0 4
login
line vty 5 15
login
!
end

SW_HQ_L1#
```
### Completed Points: 5%

<a href="#top">Back to top</a>


---

## Task 4
### SW_HQ_L2 Switch Configuration on HQ MATARAM

1. Set the hostname of the Switch with the name “SW_HQ_L2”.

```HTML        
Switch#en
Switch#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Switch(config)#hostname SW_HQ_L2
```

2. Set up VTP with Client mode, domain name "MANDALIKA" and password using "LksNTB".
        
```HTML
SW_HQ_L2(config)#vtp mode client 
Setting device to VTP CLIENT mode.
SW_HQ_L2(config)#vtp domain MANDALIKA
Changing VTP domain name from NULL to MANDALIKA
SW_HQ_L2(config)#vtp password LksNTB
Setting device VLAN database password to LksNTB
```

3. Set the IP addressing on interface VLAN 13 with the third IP address from subnet address 10.0.13.0/24.

```HTML
SW_HQ_L2(config)#int vlan 13
SW_HQ_L2(config-if)#ip address 10.0.13.3 255.255.255.0
```

4. Set the default gateway using the first IP address of the subnet address 10.0.13.0/24, one of the IP addresses on the HQ router, so that it can communicate with different networks.

```HTML
SW_HQ_L2(config)#ip default-gateway 10.0.13.1
```

5. Enable port mode to trunk for the FastEthernet0/20 and GigabitEthernet0/2 interfaces.
<b>Notes</b>: VLAN 13 is enabled as a native VLAN. 

```HTML        
SW_HQ_L2(config)#int fa0/20
SW_HQ_L2(config-if)#switchport mode trunk
SW_HQ_L2(config-if-range)#switchport trunk native vlan 13 
SW_HQ_L2(config-if-range)#switchport trunk allowed vlan 11,12,13
%LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/20, changed state to down
%LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/20, changed state to up

SW_HQ_L2(config-if)#int g0/2
SW_HQ_L2(config-if)#switchport mode trunk
SW_HQ_L2(config-if-range)#switchport trunk native vlan 13 
SW_HQ_L2(config-if-range)#switchport trunk allowed vlan 11,12,13
%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/2, changed state to down
%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/2, changed state to up
```

6. Verify the configuration that has been done to ensure that it is following the requirements
        
```HTML
SW_HQ_L2#sh run
Building configuration...

Current configuration : 1354 bytes
!
version 15.0
no service timestamps log datetime msec
no service timestamps debug datetime msec
no service password-encryption
!
hostname SW_HQ_L2
!
spanning-tree mode pvst
spanning-tree extend system-id
!
interface FastEthernet0/20
switchport trunk native vlan 13
switchport trunk allowed vlan 11-13
switchport mode trunk
!
interface GigabitEthernet0/2
switchport trunk native vlan 13
switchport trunk allowed vlan 11-13
switchport mode trunk
!
interface Vlan1
no ip address
shutdown
!
interface Vlan13
ip address 10.0.13.3 255.255.255.0
!
ip default-gateway 10.0.13.1
!
line con 0
!
line vty 0 4
login
line vty 5 15
login
!
end

SW_HQ_L2#

```
### Completed Points: 3%

<a href="#top">Back to top</a>

---

## Task 5
### Admin2 PC Configuration and Verification at HQ MATARAM

1. Set up the DHCP Client on the Admin2 PC at the MATARAM head office. <br>
Ensure the PC successfully obtains dynamic IP addressing from the DHCP Server.  

    {{< bundle-image name="PC_Admin2_DHCP.PNG" alt="PC Admin2 successfull obtained DHCP">}}

2. Verify PC Admin2 to Switch SW_HQ_L1 and Router HQ using Simple PDU. <br>
Make sure the connection is successful.

    {{< bundle-image name="SimplePDU_PC_Admin2_Switch_And_RouterHQ.PNG" alt="SimplePDU connection to SW and RouterHQ Successful">}}

### Completed Points: 0%

<a href="#top">Back to top</a>

---

## Task 6
### Wireless Controller (WLC) Configuration at HQ MATARAM

1. Perform the initial setup on the WLC HQ using the browser from the PC Admin1 desktop by entering the WLC's IP address, which is http://192.168.1.1. <br>
Create a new administrator account with the username "admin" and the password "LksNTB2021." 

    {{< bundle-image name="PC_Admin1_WLC_Setup.PNG" alt="Initial WLC Setup at PD Admin1">}}

2. Configure the Controller with the following conditions: 
    * System Name: WLC_HQ 
    * Management IP Address: using the 4th IP address from subnet 10.0.13.0/24 
    * Default Gateway: using the 1st IP address from subnet 10.0.13.0/24 
    * Management VLAN ID: 1 
    * WLAN configuration with Network Name "TEST" and Passphrase "LksNTB2021".
    
    {{< bundle-image name="WLC_HQ_Setup_Wireless_Network.PNG" alt="Wireless Setup for WLC_HQ">}}

    {{< bundle-image name="WLC_HQ_Setup_Controller.PNG" alt="Controller Setup for WLC_HQ">}}


3. After the initial setup is completed, the WLC_HQ configuration can be done through a browser from the PC_Admin2 desktop by accessing the IP address of the WLC via HTTPS, https://10.0.13.4. <br>
Login using username "admin" and password "LksNTB2021".  
    
    {{< bundle-image name="PC_admin2_WLC.PNG" alt="Access WLC from PC Admin2">}}

4. Delete the WLAN with Network Name "TEST". 
    
    {{< bundle-image name="WLC_HQ_Remove_Guest.PNG" alt="Delete WLAN TEST">}}

5. Configure a new VLAN interface on the WLC with the following requirements:  

    Interface Name  | VLAN ID | Port Number | IP Address     | Gateway
    --------        | ------  | -----       | -----         | -----
    INT-WLAN-11     | 11      | 1           | 10.0.11.254/24| 10.0.11.1
    INT-WLAN-12     | 12      | 1           | 10.0.12.254/24| 10.0.12.1

    {{< bundle-image name="WLC_HQ_VLAN11.PNG" alt="WLC_HQ VLAN 11">}}

    {{< bundle-image name="WLC_HQ_VLAN12.PNG" alt="WLC_HQ VLAN 12">}}

6. Configure the WLC to authenticate WLAN users using a RADIUS server with the subnet's second IP address, 10.0.14.0/30, and the shared secret "SMKHebat2021".
    
    {{< bundle-image name="WLC_HQ_Radius_Setup.PNG" alt="WLC_HQ Radius Setup">}}

7. Configure a new WLAN on the WLC with the following requirements: 

    Profile Name           | SSID                | ID    | Interface     | FlexConnect
    --------               | ------              | ----- | -----         | -----
    PROF-WLAN-11-HRD       | HRD-MANDALIKA       | 1     | INT-WLAN-11   | Local Switching & Local Auth
    PROF-WLAN-12-MARKETING | MARKETING-MANDALIKA | 2     | INT-WLAN-12   | Local Switching & Local Auth

    {{< bundle-image name="WLC_HQ_WLAN_11_HRD_SETUP.PNG" alt="WLC_HQ WLAN 11 HRD SETUP">}}
    {{< bundle-image name="WLC_HQ_WLAN_11_FLEX.PNG" alt="WLC_HQ_WLAN 11 FLEX SETUP">}}
    {{< bundle-image name="WLC_HQ_WLAN_11_AAA.PNG" alt="WLC_HQ_WLAN_11 AAA SETUP">}}
    
    {{< bundle-image name="WLC_HQ_WLAN_12_SETUP.PNG" alt="WLC_HQ WLAN 12 HRD SETUP">}}
    {{< bundle-image name="WLC_HQ_WLAN_12_FLEX.PNG" alt="WLC_HQ WLAN 12 FLEX SETUP">}}
    {{< bundle-image name="WLC_HQ_WLAN_12_AAA.PNG" alt="WLC_HQ WLAN 12 AAA SETUP">}}

    

8. Use WPA2-Enterprise to secure each WLAN.  
    
    {{< bundle-image name="WLC_HQ_WLAN_11_HRD_SECURITY.PNG" alt="WLC_HQ WLAN 11 SECURITY SETUP">}}
    {{< bundle-image name="WLC_HQ_WLAN_12_SECURITY.PNG" alt="WLC_HQ WLAN 12 SECURITY SETUP">}}


### Completed Points: 8%

<a href="#top">Back to top</a>

---

## Task 7
### LeighWeight Access Point (LWAP) Configuration at HQ MATARAM

1. Configure IP addressing dynamically or as a DHCP Client on LWAP_HQ_L1 and LWAP_HQ_L2. <br>
Double-check the setup to ensure that each LWAP has received dynamic IP addresses from the DHCP Server. 
    
{{< bundle-image name="LWAP_HQ_L1_DHCP.PNG" alt="LWAP_HQ_L1 DHCP Success">}}
{{< bundle-image name="LWAP_HQ_L1_DHCP_2.PNG" alt="LWAP_HQ_L1 DHCP 2 Success">}}
{{< bundle-image name="LWAP_HQ_L2_DHCP.PNG" alt="LWAP_HQ_L2 DHCP Success">}}
{{< bundle-image name="LWAP_HQ_L2_DHCP_2.PNG" alt="LWAP_HQ_L2 DHCP 2 Success">}}

2. Also, check that the CWAP Status of each LWAP indicates that it has successfully connected to the IP address of the WLC HQ and provides two WLANs, HRD-MANDALIKA and MARKETING-MANDALIKA.  
    
{{< bundle-image name="LWAP_HQ_L1_CAPWAP.PNG" alt="LWAP_HQ_L1 CAPWAP Success">}}
{{< bundle-image name="LWAP_HQ_L1_CAPWAP.PNG" alt="LWAP_HQ_L2 CAPWAP Success">}}

### Completed Points: 0%

<a href="#top">Back to top</a>

---

## Task 8
### End Device Configuration and Verification on HRD and MARKETING VLANs at HQ MATARAM

1. Set the DHCP Client on the Wireless0 interface of each End Device on VLAN HRD and MARKETING, namely Laptop_HRD1, Laptop_HRD2, Laptop_MKT1, and Laptop_MKT2, to get dynamic IP addressing from the DHCP Server. 

    Connect Laptop_HRD1 and Laptop_HRD2 in VLAN HRD to LWAP with SSID "HRD-MANDALIKA" and Wireless Security Configuration WPA2-Enterprise. <br>
Use an authentication account as mentioned in the table below: 

    Login Name  | Password  | Device Name
    --------    | ------    | -----
    hrd1        | hrd1      |Laptop_HRD1
    hrd2        | hrd2      |Laptop_HRD2
    
    {{< bundle-image name="LAPTOP_HRD1_DHCP.PNG" alt="LAPTOP_HRD1 DHCP Success">}}
    {{< bundle-image name="LAPTOP_HRD2_DHCP.PNG" alt="LAPTOP_HRD2 DHCP Success">}}

    Connect Laptop_MKT1 and Laptop_MKT2 in VLAN MARKETING to LWAP with SSID "MARKETING-MANDALIKA" with Wireless Security Configuration WPA2-Enterprise. <br>
Use an authentication account, as shown in the following table:

    Login Name  | Password  | Device Name
    --------    | ------    | -----
    mkt1        | mkt1      |Laptop_MKT1
    mkt2        | mkt2      |Laptop_MKT2

    {{< bundle-image name="LAPTOP_MKT1_DHCP.PNG" alt="LAPTOP_MKT1 DHCP Success">}}
    {{< bundle-image name="LAPTOP_MKT2_DHCP.PNG" alt="LAPTOP_MKT2 DHCP Success">}}

2. Using a simple PDU, check the connections between each laptop and the other laptop in the HQ MATARAM. <br>
Check that the connection is working properly.  

    {{< bundle-image name="SimplePDU_Laptop_11_12.PNG" alt="SimplePDU from LAPTOP_1 and LAPTOP_2">}}
    

3. Check the Internet connection of Laptop_HRD1 and Laptop_HRD2 in VLAN MARKETING by sending an email to external@gmail.com.<br>
The title and email message used for testing can be chosen independently.<br>
Check that the email is properly sent from Laptop HRD1 and Laptop HRD2. 
    
    {{< bundle-image name="Send_Email_External_Gmail_Laptop_HRD1_2.PNG" alt="LAPTOP_HRD1 and LAPTOP_HRD2 send email to external@gmail.com">}}

4. Verify that the emails sent from Laptop_HRD1 and Laptop_HRD2 were successfully received via PC Client Internet on the Internet subnet.<br>
Respond to each of these emails.

    {{< bundle-image name="Receive_Email.PNG" alt="PC Client Internet successful receive email from LAPTOP_1 and LAPTOP_2">}}

5. Verify that the email has been downloaded successfully from Laptop_HRD1 and Laptop_HRD2.<br>
    
    {{< bundle-image name="Receive_Reply_Email.PNG" alt="LAPTOP_HRD1 and LAPTOP_HRD2 successful receive email from PC Client Internet">}}

6. Verify the Internet connection of Laptop_MKT1 and Laptop_MKT2 in VLAN MARKETING using a browser by visiting ntbprov.go.id, ditpsmk.net, gmail.com, and universitasbumigora.ac.id.<br>
Check that the connection is working properly.  
    
    {{< bundle-image name="Verify_Connection_To_GMAIL_And_UnivBumiGora_V1.PNG" alt="LAPTOP_MKT1 and Laptop_MKT2 successful access gmail.com and universitasbumigora.ac.id">}}
    
    {{< bundle-image name="Verify_Connection_To_NTBPROV_And_DITP_V1.PNG" alt="LAPTOP_MKT1 and Laptop_MKT2 successful access ntbprov.go.id and ditpsmk.net">}}    

### Completed Points: 0%

<a href="#top">Back to top</a>

---

## Task 9
### BRANCH SUMBAWA Server Configuration
1. Set the IP addressing and default gateway and DNS on the BRANCH Server with the following conditions:
    
    * IP Address: the fourth IP address of subnet 10.0.15.0/29.

    {{< bundle-image name="IP-Subnet-Server-Branch-Sumbawa.PNG" alt="IP address at BRANCH SUMBAWA">}}    
     
    * Default Gateway: the first IP address of subnet 10.0.15.0/29.
    * DNS Server: 192.0.2.1

    {{< bundle-image name="Gateway-DNS-HQ-Server-Branch-Sumbawa.PNG" alt="Default Gateway and DNS at BRANCH SUMBAWA">}}

2. Enable HTTP and HTTPS services.

    {{< bundle-image name="HTTP-HTTPS-Server-Branch-Sumbawa.PNG" alt="Enable HTTP and HTTPS services">}}

3. Enable AAA service and configure Network Configuration with Server Radius type and User Setup as given in the table below:

    Client Name  | Client IP                            | Key
    --------     | ------                               | -----
    BRANCH       | 10.0.15.1                            | NTBJawara2021
    SW_BRANCH    | 10.0.15.2                            | NTBHebat2021
    WRT_BRANCH   | 10.0.15.3                            | SMKBisa2021

    <br>

    Username            | Password
    --------            |------
    sales1              | sales1
    sales2              | sales2
    engineer1           | engineer1
    engineer2           | engineer2
    adminBRANCH         | LksNTB2021
    helpdeskBRANCH      | mandalika

    {{< bundle-image name="AAA-Server-Branch-Sumbawa.PNG" alt="Enable AAA services">}}

### Completed Points: 10%

<a href="#top">Back to top</a>

---

## Task 10
### BRANCH SUMBAWA Router Configuration

1. Set the router’s hostname with the name “BRANCH”.

```HTML
Router>en
Router#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Router(config)#hostname BRANCH
 ```
        
2. Set the encapsulation protocol on the Serial0/0/1 interface connected to the ISP router through PPP and use PPP authentication using CHAP with the password “LksNTB”.

```HTML
BRANCH(config)#int s0/0/1
BRANCH(config-if)#encapsulation ppp
BRANCH(config-if)#ppp authentication chap
BRANCH(config-if)#exit
BRANCH(config)#username ISP password LksNTB
```

3. Configure the IP addressing on the Serial0/0/1 interface connected to the ISP router with the second IP address from subnet 192.0.2.24/29.

```HTML
BRANCH(config)#int s0/0/1
BRANCH(config-if)#ip address 192.0.2.26 255.255.255.248
BRANCH(config-if)#no shut
%LINK-5-CHANGED: Interface Serial0/0/1, changed state to up
%LINEPROTO-5-UPDOWN: Line protocol on Interface Serial0/0/1, changed state to up
```

4. Create a subinterface on the GigabitEthernet0/0 interface that implements the IEEE 802.1Q encapsulation protocol and configure the router-on-stick for VLAN communication.          
    The following conditions are used to assign IP addresses to each subinterface: 

    * Subinterface GigabitEthernet0/0.1 for VLAN 1 NATIVE with the first IP address of the subnet address 10.0.15.0/29. 
    * Subinterface GigabitEthernet0/0.2 for VLAN 2 SALES with the first IP address of the subnet address 10.0.15.16/28. 

```HTML
BRANCH(config)#int g0/0
BRANCH(config-if)#description ROUTER-ON-STICK
BRANCH(config-if)#no shut
%LINK-5-CHANGED: Interface GigabitEthernet0/0, changed state to up
%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0, changed state to up

%LINK-5-CHANGED: Interface GigabitEthernet0/0.1, changed state to up
BRANCH(config)#int g0/0.1
%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0.1, changed state to up
BRANCH(config-subif)#encapsulation dot1Q 1
BRANCH(config-subif)#ip address 10.0.15.1 255.255.255.248

BRANCH(config)#int g0/0.2
%LINK-5-CHANGED: Interface GigabitEthernet0/0.2, changed state to up
%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0.2, changed state to up
BRANCH(config-subif)#encapsulation dot1Q 2
BRANCH(config-subif)#ip address 10.0.15.17 255.255.255.240
BRANCH(config-subif)#description SALES
BRANCH(config-subif)#exit
```

6. Creating a DHCP Server:
   * Pool name "SALES" for VLAN 11 with subnet 10.0.15.16/28.

    TCP/IP parameters are set:

     * The pool includes the default gateway, which uses the subnet's initial IP address, 10.0.15.16/28.
     * DNS server at 192.0.2.1. 
     * Set the first IP address of subnet 10.0.15.16/28 to not be leased to DHCP Client.

```HTML
BRANCH(config)#ip dhcp pool SALES
BRANCH(dhcp-config)#network 10.0.15.16 255.255.255.240
BRANCH(dhcp-config)#default-router 10.0.15.17
BRANCH(dhcp-config)#dns-server 192.0.2.1
BRANCH(config)#ip dhcp excluded-address 10.0.15.17
```

7. Set the default route to the ISP using the gateway of the first IP address in the subnet 192.0.2.24/29 used by the ISP router’s Serial0/0/1 interface. 

```HTML
BRANCH(config)#ip route 0.0.0.0 0.0.0.0 192.0.2.25    
```

8. Create a Standard Named ACL called "ALLOW-INTERNET" that permits Internet access to all hosts on subnet addresses 10.0.15.0/29 and 10.0.15.16/28.

```HTML 
BRANCH(config)#ip access-list standard ALLOW-INTERNET
BRANCH(config-std-nacl)#permit 10.0.15.0 0.0.0.7
BRANCH(config-std-nacl)#permit 10.0.15.16 0.0.0.15
```

9. Set NAT Overload or Port Address Translation (PAT) for Standard Named ACL “ALLOW-INTERNET”.

```HTML    
BRANCH(config)#int s0/0/1
BRANCH(config-if)#ip nat outside
BRANCH(config)#int ra g0/0.1 - g0/0.2
BRANCH(config-if-range)#ip nat inside
BRANCH(config)#ip nat inside source list ALLOW-INTERNET interface s0/0/1 overload
```

10. Set static NAT so that ONLY HTTP and HTTPS services on the BRANCH server with Private IP address 10.0.15.4 are translated to Public IP address 192.0.2.27.

```HTML   
BRANCH(config)#ip nat inside source static 10.0.15.4 192.0.2.27
```
11. Set up the BRANCH router to synchronize time with the NTP Server at IP address 192.0.2.2 and use md5 authentication with key 46 and password “NTBHebat2021”.

```HTML
BRANCH(config)#ntp server 192.0.2.2 key 46
BRANCH(config)#ntp authentication-key 46 md5 NTBHebat2021
BRANCH(config)#ntp trusted-key 46
```    

12. Set the privilege mode password with the secret “LksNTB2021”.

```HTML    
BRANCH(config)#enable secret LksNTB2021
```    

13. Set the DNS server of the BRANCH router with IP address 192.0.2.1.

```HTML    
BRANCH(config)#ip name-server 192.0.2.1
```

14. Enable SSH version 2 on the BRANCH router with the domain name "branch.mandalika.id" and create RSA keys with modulus 1024. 

```HTML 
BRANCH(config)#ip domain-name branch.mandalika.id
BRANCH(config)#ip ssh version 2
BRANCH(config)#crypto key generate rsa general-keys modulus 1024
% You already have RSA keys defined named BRANCH.branch.mandalika.id
% They will be replaced.

% The key modulus size is 1024 bits
% Generating 1024 bit RSA keys, keys will be non-exportable...[OK]
%SSH-5-ENABLED: SSH 2 has been enabled
```  

15. Create a local authentication account on the BRANCH router with username “admin” and password “LksNTB2021”.
This account is a backup when authentication to the Radius server has problems.

```HTML
BRANCH(config)#username admin privilege 15 secret LksNTB2021
BRANCH(config)#line vty 0 4
BRANCH(config-line)#login local
BRANCH(config-line)#transport input ssh
BRANCH(config)#line console 0
BRANCH(config-line)#login local
BRANCH(config)#line aux 0
BRANCH(config-line)#login local
```        
16. Enabled the AAA feature on the BRANCH router and set the default authentication to use the “local” database.

```HTML
BRANCH(config)#aaa new-model 
BRANCH(config)#aaa authentication login default local
```

17. Creating a user login authentication list with the name “SERVER-AAA” that uses the Radius authentication method first, followed by “local”. 

```HTML
BRANCH(config)#aaa authentication login SERVER-AAA group RADIUS local 
```

18. Connect to the RADIUS Server with the fourth IP address from subnet 10.0.15.0/29 and use the secret "NTBJawara2021".

```HTML
BRANCH(config)#RADIUS server SERVER-AAA
BRANCH(config-RADIUS-server)#address ipv4 10.0.15.4 auth-port 1645
BRANCH(config-RADIUS-server)#key NTBJawara2021
```

19. Set the BRANCH router to only accept remote access via SSH with a maximum number of sessions for 5 (five) users simultaneously and using the authentication list “SERVER-AAA”.

BRANCH(config)#line vty 0 4
 ```HTML   
BRANCH(config-line)#login authentication SERVER-AAA
BRANCH(config-line)#session-limit 5
 ```       

20. Set the console access to the router also to use the authentication list “SERVER-AAA” AUTHENTICATION LIST. 

```HTML    
BRANCH(config)#line con 0
BRANCH(config-line)#login authentication SERVER-AAA
```

21. Verify the configuration that has been done to ensure that it is by the requirements. 

```HTML
BRANCH#sh run
Building configuration...

Current configuration : 2271 bytes
!
version 15.1
no service timestamps log datetime msec
no service timestamps debug datetime msec
no service password-encryption
!
hostname BRANCH
!
enable secret 5 $1$mERr$ix/GaeUGI2eMcE7/bu9c2/
!
ip dhcp excluded-address 10.0.15.17
!
ip dhcp pool SALES
network 10.0.15.16 255.255.255.240
default-router 10.0.15.17
dns-server 192.0.2.1
!
aaa new-model
!
aaa authentication login SERVER-AAA group RADIUS local 
aaa authentication login default local 
!
no ip cef
no ipv6 cef
!
username ISP password 0 LksNTB
username admin privilege 15 secret 5 $1$mERr$MmdodDsCCQflw.d5M6q3C.
!
license udi pid CISCO1941/K9 sn FTX15243J7P-
license boot module c1900 technology-package securityk9
!
ip ssh version 2
ip domain-name branch.mandalika.id
ip name-server 192.0.2.1
!
spanning-tree mode pvst
!
interface GigabitEthernet0/0
description ROUTER-ON-STICK
no ip address
ip nat inside
duplex auto
speed auto
!
interface GigabitEthernet0/0.1
encapsulation dot1Q 1 native
ip address 10.0.15.1 255.255.255.248
ip nat inside
!
interface GigabitEthernet0/0.2
description SALES
encapsulation dot1Q 2
ip address 10.0.15.17 255.255.255.240
ip nat inside
!
interface GigabitEthernet0/1
no ip address
duplex auto
speed auto
shutdown
!
interface Serial0/0/0
no ip address
clock rate 2000000
shutdown
!
interface Serial0/0/1
description TO ISP
ip address 192.0.2.26 255.255.255.248
encapsulation ppp
ppp authentication chap
ip nat outside
!
interface Vlan1
no ip address
shutdown
!
ip nat inside source list ALLOW-INTERENT interface Serial0/0/1 overload
ip nat inside source static 10.0.15.4 192.0.2.27 
ip classless
ip route 0.0.0.0 0.0.0.0 192.0.2.25 
!
ip flow-export version 9
!
ip access-list standard ALLOW-INTERNET
permit 10.0.15.0 0.0.0.7
permit 10.0.15.16 0.0.0.15
!
RADIUS server SERVER-AAA
address ipv4 10.0.15.4 auth-port 1645
key NTBJawara2021
RADIUS server 10.0.15.4
address ipv4 10.0.15.4 auth-port 1645
key NTBJawara2021
!
line con 0
login authentication SERVER-AAA
!
line aux 0
!
line vty 0 4
session-limit 5
login authentication SERVER-AAA
transport input ssh
!
ntp authentication-key 46 md5 080F786C211C071606595C567B 7
ntp trusted-key 46
ntp server 192.0.2.2 key 46
!
end

BRANCH#
```        
### Completed Points: 11% 

<a href="#top">Back to top</a>

---

## Task 11
### SW_BRANCH Switch Configuration at BRANCH SUMBAWA

1. Set the hostname of the Switch with the name “SW_BRANCH”.

```HTML
Switch>en
Switch#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Switch(config)#hostname SW_BRANCH
```

2. Set the IP addressing on the VLAN 1 interface with the second IP address of the subnet address 10.0.15.0/29. 

```HTML
SW_BRANCH(config)#int vlan 1
SW_BRANCH(config-if)#ip address 10.0.15.2 255.255.255.248
SW_BRANCH(config-if)#no shut
%LINK-5-CHANGED: Interface Vlan1, changed state to up
%LINEPROTO-5-UPDOWN: Line protocol on Interface Vlan1, changed state to up
```        

3. Set the default gateway using the first IP address of the subnet address 10.0.15.0/24, one of the IP addresses on the HQ router, so that it can communicate with different networks. 

```HTML
SW_BRANCH(config)#ip default-gateway 10.0.15.1
```

4. Set 802.1x authentication to use a Radius AAA server with the fourth IP address of subnet 10.0.15.0/29, port 1645, and secret "NTBHebat2021."  

```HTML
SW_BRANCH(config)#dot1x system-auth-control 
SW_BRANCH(config)#aaa new-model
SW_BRANCH(config)#aaa authentication dot1x default group RADIUS 
SW_BRANCH(config)#RADIUS-server host 10.0.15.4 auth-port 1645 key NTBHebat2021       
```

5. Create a new VLAN with ID 2 and the name SALES.  

```HTML
SW_BRANCH(config)#vlan 2
SW_BRANCH(config-vlan)#name SALES
```

6. Assign FastEthernet0/1 and FastEthernet0/2 ports to be members of VLAN 2. 

```HTML
SW_BRANCH(config)#int ra fa0/1 - fa0/2
SW_BRANCH(config-if-range)#switchport mode access
SW_BRANCH(config-if-range)#switchport access vlan 2
```

7. Assign FastEthernet0/1 and FastEthernet0/2 ports to run EAPoL (802.1x).

```HTML
SW_BRANCH(config)#int ra fa0/1 - fa0/2
SW_BRANCH(config-if-range)#authentication port-control auto
SW_BRANCH(config-if-range)#dot1x pae authenticator
```

8. Enable trunk port mode for the GigabitEthernet0/2 interface attached to the BRANCH SUMBAWA router. 

```HTML
SW_BRANCH(config)#int g0/2
SW_BRANCH(config-if)#switchport mode trunk 
SW_BRANCH(config-if)#description BRANCH SUMBAWA
```

9. Verify the configuration that has been done to ensure that it is following the requirements. 

```HTML
SW_BRANCH#
%SYS-5-CONFIG_I: Configured from console by console

SW_BRANCH#sh run 
Building configuration...

Current configuration : 1551 bytes
!
version 15.0
no service timestamps log datetime msec
no service timestamps debug datetime msec
no service password-encryption
!
hostname SW_BRANCH
!
aaa new-model
!
aaa authentication dot1x default group RADIUS 
!
dot1x system-auth-control
spanning-tree mode pvst
spanning-tree extend system-id
!
interface FastEthernet0/1
switchport access vlan 2
switchport mode access
authentication port-control auto
dot1x pae authenticator
!
interface FastEthernet0/2
switchport access vlan 2
switchport mode access
authentication port-control auto
dot1x pae authenticator
!
interface GigabitEthernet0/2
description BRANCH SUMBAWA
switchport mode trunk
!
interface Vlan1
ip address 10.0.15.2 255.255.255.248
!
ip default-gateway 10.0.15.1
!
RADIUS-server host 10.0.15.4 auth-port 1645 key NTBHebat2021
!
line con 0
login
!
line vty 0 4
line vty 5 15
!
end

SW_BRANCH#
```

### Completed Points: 3%

<a href="#top">Back to top</a>

---

## Task 12
### DHCP Client Configuration and Internet Connection Verification on VLAN 2 SALES PC at BRANCH SUMBAWA

1. Configure each PC in VLAN 2 SALES Sumbawa BRANCH as a DHCP client and enable 802.1x security features with authentication accounts, as shown in the table below: 

    PC          | User    | Key
    --------    | ------  | -----
    PC_Sales1   | sales1  | sales1
    PC_Sales2   | sales2  | sales2

2. Verify that each PC on each VLAN has received a dynamic IP address from the DHCP Server.

    {{< bundle-image name="PC_Sales1_DHCP_802.1X.PNG" alt="DHCP PC_Sales1 using 802.1X Auth">}}
    {{< bundle-image name="PC_Sales2_DHCP_802.1X.PNG" alt="DHCP PC_Sales2 using 802.1X Auth">}}

3. Using Simple PDU, verify the connection between PC SALES1 and PC SALES2 as well as the BRANCH SUMBAWA router.<br>
Check that the connection is working properly.

    {{< bundle-image name="SimplePDU_Client_To_Router_Success.PNG" alt="Simple PDU from Client to Router">}}

4. Use a browser to test the Internet connection of each PC in VLAN 2 SALES by visiting ntbprov.go.id, ditpsmk.net, gmail.com, and universitasbumigora.ac.id.<br>
Check that the connection is working properly.
    
    {{< bundle-image name="Verify_Connection_To_NTBPROV_And_DITP.PNG" alt="Client access to ntbprov.go.id and ditpsmk.net is success">}}
    {{< bundle-image name="Verify_Connection_To_GMAIL_And_UnivBumiGora.PNG" alt="Client access to gmail.com and universitasbumigora.ac.id is success">}}

5. After completing the NAT configuration on the HQ MATARAM router, access to the HQ Server can be verified by visiting "mandalika.id"<brs>
Check that the connection is working properly.  
    
    {{< bundle-image name="Access_PC_SALES_TO_mandalika.PNG" alt="Client access to mandalika.id is success">}}

### Completed Points: 0%

<a href="#top">Back to top</a>

---

## Task 13
### WRT_BRANCH Wireless Router Configuration at BRANCH SUMBAWA

1. In Internet Setup, configure the IP addressing statically or manually using the following conditions:

   * Use the third IP address from subnet 10.0.15.0/29.
   * The default gateway uses the first IP address of subnet 10.0.15.0/29.
   * The DNS server is using 192.0.2.1.
    
    {{< bundle-image name="WRT_BRANCH_Internet_Setup.PNG" alt="WRT Internet Setup">}}

2. In Network Setup, configure the IP addressing statically or manually to the first IP address in subnet 10.0.15.128/25. 
    
    {{< bundle-image name="WRT_BRANCH_Network_Setup.PNG" alt="WRT Network Setup">}}

3. Enable the DHCP Server feature and assign dynamically distributed IP addresses to DHCP Clients beginning with the first IP address in subnet 10.0.15.128/25 for a maximum of 100 users.<br>
The DNS Server option is set to 192.0.2.1.

    {{< bundle-image name="WRT_BRANCH_DHCP_Setup.PNG" alt="WRT DHCP Setup">}}


4. Configure the Wireless feature with the following conditions:
    
    * Network mode "Wireless-G Only"
    * Wireless network identifier name or SSID "ENGINEER-MANDALIKA"
    * Channel 6
    * Enable the SSID Broadcast feature.
    * WPA2 Enterprise security mode with AES encryption
    * Radius Server uses the fourth IP address from subnet 10.0.15.0/29 with the shared secret "SMKBisa2021".

    {{< bundle-image name="WRT_BRANCH_Wireless_Setup.PNG" alt="WRT Wireless Setup">}}
    {{< bundle-image name="WRT_BRANCH_Wireless_Security.PNG" alt="WRT Security Setup">}}


### Completed Points: 4%

<a href="#top">Back to top</a>

---

## Task 14
### End Device Configuration and Verification at WLAN ENGINEER BRANCH SUMBAWA  

1. Set the DHCP Client on the Wireless0 interface of each End Device on the WLAN ENGINEER subnet, namely Tablet_ENGINEER1 and Tablet_ENGINEER2, to get dynamic IP addressing from the DHCP Server.

Connect each Tablet on the ENGINEER WLAN subnet to the Wireless Access Point (AP) with the SSID "ENGINEER-MANDALIKA" and the Wireless Security Configuration WPA2-Enterprise.<br>
Use an authentication account as mentioned in the table below: 

Device          | Login Name| Password
--------        | ------    | -----
Tablet_ENGINEER1| engineer1 | engineer1
Tablet_ENGINEER2| engineer2 | engineer2 

{{< bundle-image name="Tablet_Engineer1.PNG" alt="Tablet_Engineer1 Setup">}}
{{< bundle-image name="Tablet_Engineer2.PNG" alt="Tablet_Engineer2 Setup">}}


### Completed Points: 2%

<a href="#top">Back to top</a>

---

## Task 15
### VPN Configuration on an HQ MATARAM Router utilizing GRE over IPSec and OSPF

1. Create an Extended Named ACL with the name "INTERESTING-TRAFFIC" for:

   * Allow GRE traffic from source IP address 192.0.2.18 to destination IP address 192.0.2.26.
   * Identify IP traffic from HQ MATARAM Server, 10.0.14.2, to VLAN 2 SALES on BRANCH SUMBAWA router with subnet address 10.0.15.0/29 to the IP address as interesting traffic.
   * Identify as interesting traffic IP traffic from VLAN 11 HRD at HQ with subnet address 10.0.11.0/24 to BRANCH Server with address 10.0.15.4.
   * Identify as interesting traffic IP traffic from VLAN 12 MARKETING at HQ with subnet address 10.0.12.0/24 to the BRANCH Server with address 10.0.15.4. 

```HTML
HQ(config)#ip access-list extended INTERESTING-TRAFFIC
HQ(config-ext-nacl)#permit gre host 192.0.2.18 host 192.0.2.26
HQ(config-ext-nacl)#permit tcp 10.0.15.0 0.0.0.7 host 10.0.14.2
HQ(config-ext-nacl)#permit tcp 10.0.11.0 0.0.0.255 host 10.0.15.4
HQ(config-ext-nacl)#permit tcp 10.0.12.0 0.0.0.255 host 10.0.15.4
```   

2. Configure IKE Phase 1 ISAKMP policy with priority 46 and IKE Phase 2 IPSec policy with sequence 46 on the HQ MATARAM router using the conditions shown in table A & B below: 

<p style="text-align: center;"> Table A. Parameter ISAKMP Phase  1 Policy</p>

Parameter              | Router HQ MATARAM  
--------               | ------             
Key Distribution Method | ISAKMP             
Encryption Algorithm    | AES 256            
Hash Algorithm         | SHA-1               
Authentication Method  | pre-share          
Key Exchange           | DH 5               
ISAKMP Key             | Mandalika          
    
<br>

```HTML
HQ(config)#crypto isakmp policy 46
HQ(config-isakmp)#encr aes 256
HQ(config-isakmp)#hash sha
HQ(config-isakmp)#authentication pre-share 
HQ(config-isakmp)#group 5 
HQ(config)#crypto isakmp key Mandalika address 192.0.2.26
```

<p style="text-align: center;"> Table B. Parameter ISAKMP Phase 2 Policy</p> 
    

Parameter                    | Router HQ MATARAM                        
--------                     | ------                                   
Transform Set Name           | HQ-SET                                   
ESP Transform Encryption     | esp-aes                                  
ESP Transform Authentication | esp-sha-hmac                              
Peer IP Address              | 192.0.2.26                               
Traffic to be Encrypted      | Extended Named ACL "INTERESTING-TRAFFIC"   
Crypto Map Name              | HQ-MAP                                   
SA Establishment             | ipsec-isakmp                             

<br>

```HTML    
HQ(config)#crypto ipsec transform-set HQ-SET esp-aes esp-sha-hmac 
HQ(config)#crypto map HQ-MAP 46 ipsec-isakmp 
% NOTE: This new crypto map will remain disabled until a peer 
and a valid access list have been configured.
HQ(config-crypto-map)#set peer 192.0.2.26
HQ(config-crypto-map)#set transform-set HQ-SET
HQ(config-crypto-map)#match address INTERESTING-TRAFFIC
```

3. Configure the crypto map on the outgoing Serial0/0/0 interface.

```HTML        
HQ(config)#int s0/0/0
HQ(config-if)#crypto map HQ-MAP
*Jan  3 07:16:26.785: %CRYPTO-6-ISAKMP_ON_OFF: ISAKMP is ON
```

4. Configure a GRE Tunnel over IPSec with the following parameters:

    * Use the first IP address of the subnet 10.0.14.4/30 to set the IP address of the tunnel interface 46.
    * Configure interface Serial0/0/0 as the source and IP address 192.0.2.26 as the destination for tunnel interface 46's endpoint.

```HTML
HQ(config)#int tunnel 46
%LINK-5-CHANGED: Interface Tunnel46, changed state to up
HQ(config-if)#ip address 10.0.14.5 255.255.255.252
HQ(config-if)#tunnel source serial 0/0/0
HQ(config-if)#tunnel destination 192.0.2.26
HQ(config-if)#tunnel mode gre ip 
```

5. Add the subnet addresses 10.0.14.0/30, 10.0.14.5/30, 10.0.11.0/24, and 10.0.12.0/24 as part of the OSPF area 0 network in the OSPF routing protocol process-id 46.

```HTML
HQ(config)#router ospf 46
HQ(config-router)#default-information originate 
HQ(config-router)#log-adjacency-changes 
HQ(config-router)#network 10.0.14.0 0.0.0.3 area 0
HQ(config-router)#network 10.0.14.5 0.0.0.3 area 0
HQ(config-router)#network 10.0.11.0 0.0.0.255 area 0
HQ(config-router)#network 10.0.12.0 0.0.0.255 area 0
```    

6. Verify the configuration to confirm that it complies with the requirements.  

```HTML
HQ#sh run
Building configuration...

Current configuration : 3814 bytes
!
version 15.1
no service timestamps log datetime msec
no service timestamps debug datetime msec
no service password-encryption
!
hostname HQ
!
enable secret 5 $1$mERr$ix/GaeUGI2eMcE7/bu9c2/
!
ip dhcp excluded-address 10.0.11.1
ip dhcp excluded-address 10.0.12.1
ip dhcp excluded-address 10.0.13.1 10.0.13.4
!
ip dhcp pool HRD
network 10.0.11.0 255.255.255.0
default-router 10.0.11.1
dns-server 192.0.2.1
ip dhcp pool MARKETING
network 10.0.12.0 255.255.255.0
default-router 10.0.12.1
dns-server 192.0.2.1
ip dhcp pool NATIVE
network 10.0.13.0 255.255.255.0
default-router 10.0.13.1
dns-server 192.0.2.1
!
aaa new-model
!
aaa authentication login SERVER-AAA group RADIUS local 
aaa authentication login default local 
!
no ip cef
no ipv6 cef
!
username ISP password 0 LksNTB
username admin privilege 15 secret 5 $1$mERr$ix/GaeUGI2eMcE7/bu9c2/
!
license udi pid CISCO1941/K9 sn FTX1524O1D2-
license boot module c1900 technology-package securityk9
!
crypto isakmp policy 46
encr aes 256
authentication pre-share
group 5
!
crypto isakmp key Mandalika address 192.0.2.26
!
crypto ipsec transform-set HQ-SET esp-aes esp-sha-hmac
!
crypto map HQ-MAP 46 ipsec-isakmp 
set peer 192.0.2.26
set transform-set HQ-SET 
match address INTERESTING-TRAFFIC
!
ip ssh version 2
ip domain-name mandalika.id
ip name-server 192.0.2.1
!
spanning-tree mode pvst
!
interface Tunnel46
ip address 10.0.14.5 255.255.255.252
mtu 1476
tunnel source Serial0/0/0
tunnel destination 192.0.2.26
!
interface GigabitEthernet0/0
description ROUTER-ON-STICK
no ip address
duplex auto
speed auto
!
interface GigabitEthernet0/0.11
description VLAN 11 HRD
encapsulation dot1Q 11
ip address 10.0.11.1 255.255.255.0
ip nat inside
!
interface GigabitEthernet0/0.12
description VLAN 12 MARKETING
encapsulation dot1Q 12
ip address 10.0.12.1 255.255.255.0
ip nat inside
!
interface GigabitEthernet0/0.13
description VLAN 13 NATIVE
encapsulation dot1Q 13 native
ip address 10.0.13.1 255.255.255.0
ip nat inside
!
interface GigabitEthernet0/1
description TO HQ SERVER
ip address 10.0.14.1 255.255.255.252
ip nat inside
duplex auto
speed auto
!
interface Serial0/0/0
description TO ISP
ip address 192.0.2.18 255.255.255.248
encapsulation ppp
ppp authentication chap
ip nat outside
crypto map HQ-MAP
!
interface Serial0/0/1
no ip address
clock rate 2000000
shutdown
!
interface Vlan1
no ip address
shutdown
!
router ospf 46
log-adjacency-changes
network 10.0.14.0 0.0.0.3 area 0
network 10.0.14.4 0.0.0.3 area 0
network 10.0.11.0 0.0.0.255 area 0
network 10.0.12.0 0.0.0.255 area 0
default-information originate
!
ip nat inside source list ALLOW-INTERNET interface Serial0/0/0 overload
ip nat inside source static 10.0.14.2 192.0.2.19 
ip classless
ip route 0.0.0.0 0.0.0.0 192.0.2.17 
!
ip flow-export version 9
!
ip access-list extended ALLOW-INTERNET
permit udp any host 192.0.2.1 eq domain
permit tcp 10.0.11.0 0.0.0.255 host 192.0.2.5 eq smtp
permit tcp 10.0.11.0 0.0.0.255 host 192.0.2.5 eq pop3
permit tcp 10.0.12.0 0.0.0.255 any
permit tcp 10.0.13.0 0.0.0.255 any
ip access-list extended INTERESTING-TRAFFIC
permit gre host 192.0.2.18 host 192.0.2.26
permit ip 10.0.15.0 0.0.0.7 host 10.0.14.2
permit ip 10.0.11.0 0.0.0.255 host 10.0.15.4
permit ip 10.0.12.0 0.0.0.255 host 10.0.15.4
!
no cdp run
!
RADIUS server SERVER-AAA
address ipv4 10.0.14.2 auth-port 1645
key NTBJawara2021
RADIUS server 10.0.14.2
address ipv4 10.0.14.2 auth-port 1645
key NTBJawara2021
!
line con 0
login authentication SERVER-AAA
!
line aux 0
!
line vty 0 4
session-limit 5
login authentication SERVER-AAA
transport input ssh
!
ntp authentication-key 46 md5 080F786C211C071606595C567B 7
ntp trusted-key 46
ntp server 192.0.2.2 key 46
!
end

HQ#
```        

### Completed Points: 8%

<a href="#top">Back to top</a>

---

## Task 16
### VPN Configuration on an BRANCH SUMBAWA Router utilizing GRE over IPSec and OSPF

1. Create an Extended Named ACL with the name "INTERESTING-TRAFFIC" for: 
   * Allow GRE traffic from source IP address 192.0.2.26 to destination IP address 192.0.2.18. 
   * Identify as interesting traffic IP traffic from VLAN 2 SALES on the BRANCH SUMBAWA router with subnet address 10.0.15.0/29 to the IP address of the HQ MATARAM Server, which is 10.0.14.2.
   * Identify as interesting traffic IP traffic from the BRANCH Server with address 10.0.15.4 to VLAN 11 HRD with subnet address 10.0.11.0/24 at HQ MATARAM. 
   * Identify as interesting traffic IP traffic from the BRANCH Server with address 10.0.15.4 to VLAN 12 MARKETING with subnet address 10.0.12.0/24 at HQ MATARAM.  

```HTML
BRANCH(config)#ip access-list extended INTERESTING-TRAFFIC    
BRANCH(config-ext-nacl)#permit gre host 192.0.2.26 host 192.0.2.18
BRANCH(config-ext-nacl)#permit ip 10.0.15.0 0.0.0.7 host 10.0.14.2
BRANCH(config-ext-nacl)#permit ip 10.0.11.0 0.0.0.255 host 10.0.15.4
BRANCH(config-ext-nacl)#permit ip 10.0.12.0 0.0.0.255 host 10.0.15.4
```

2. Configure IKE Phase 1 ISAKMP policy with priority 46 and IKE Phase 2 IPSec policy with sequence 46 on the BRANCH SUMBAWA router using the conditions shown in table A & B below:  

<p style="text-align: center;"> Table A. Parameter ISAKMP Phase 1 Policy</p>

Parameter               | ROUTER BRANCH SUMBAWA
--------                | -----
Key Distribution Method  | ISAKMP
Encryption Algorithm    | AES256
Hash Algorithm          | SHA-1 
Authentication Method   | pre-share
Key Exchange            | DH 5
ISAKMP Key              | Mandalika
    
<br>

```HTML
BRANCH(config)#crypto isakmp policy 46
BRANCH(config-isakmp)#encr aes 256
BRANCH(config-isakmp)#hash sha
BRANCH(config-isakmp)#authentication pre-share 
BRANCH(config-isakmp)#group 5 
BRANCH(config)#crypto isakmp key Mandalika address 192.0.2.18
```

<p style="text-align: center;"> Table B. Parameter ISAKMP Phase 2 Policy</p> 
    

Parameter                           | ROUTER BRANCH SUMBAWA
--------                            | -----
Transform Set Name                  | BRANCH-SET
ESP Transform Encryption            | esp-aes
ESP Transform Authentication        | SHA-1 
Peer IP Address                     | 192.0.2.18
Traffic to be Encrypted             | Extended Named ACL "INTERESTING-TRAFFIC"  
Crypto Map Name                     | BRANCH-MAP
SA Establishment                    | ipsec-isakmp

<br>

```HTML
BRANCH(config)#crypto ipsec transform-set BRANCH-SET esp-aes esp-sha-hmac 
BRANCH(config)#crypto map BRANCH-MAP 46 ipsec-isakmp 
% NOTE: This new crypto map will remain disabled until a peer 
and a valid access list have been configured.
BRANCH(config-crypto-map)#set peer 192.0.2.18
BRANCH(config-crypto-map)#set transform-set BRANCH-SET
BRANCH(config-crypto-map)#match address INTERESTING-TRAFFIC
```

4. Configure the crypto map on the outgoing Serial0/0/1 interface. 

```HTML
BRANCH(config)#int s0/0/1
BRANCH(config-if)#crypto map BRANCH-MAP
*Jan  3 07:16:26.785: %CRYPTO-6-ISAKMP_ON_OFF: ISAKMP is ON
```
    
5. Configure a GRE Tunnel over IPSec with the following parameters: 

    * Use the second IP address of the subnet 10.0.14.4/30 to set the IP address of the tunnel interface 46.
    * Configure interface Serial0/0/1 as the source and IP address 192.0.2.26 as the destination for tunnel interface 46’s endpoint.. 

```HTML
BRANCH(config)#int tunnel 46
%LINK-5-CHANGED: Interface Tunnel46, changed state to up
BRANCH(config-if)#ip address 10.0.14.6 255.255.255.252
BRANCH(config-if)#tunnel source serial 0/0/1
BRANCH(config-if)#tunnel destination 192.0.2.18
BRANCH(config-if)#tunnel mode gre ip 
```
6. Add the subnet addresses 10.0.14.0/30, 10.0.15.0/29 and 10.0.15.16/28 as part of the OSPF area 0 network in the OSPF routing protocol process-id 46.

```HTML
BRANCH(config)#router ospf 46
BRANCH(config-router)#default-information originate 
BRANCH(config-router)#log-adjacency-changes 
BRANCH(config-router)#network 10.0.14.4 0.0.0.3 area 0
BRANCH(config-router)#network 10.0.15.0 0.0.0.7 area 0
BRANCH(config-router)#network 10.0.15.16 0.0.0.15 area 0
```

7. Verify the configuration to confirm that it complies with the requirements.

```HTML
BRANCH#sh run
Building configuration...

Current configuration : 3133 bytes
!
version 15.1
no service timestamps log datetime msec
no service timestamps debug datetime msec
no service password-encryption
!
hostname BRANCH
!
enable secret 5 $1$mERr$ix/GaeUGI2eMcE7/bu9c2/
!
ip dhcp excluded-address 10.0.15.17
!
ip dhcp pool SALES
network 10.0.15.16 255.255.255.240
default-router 10.0.15.17
dns-server 192.0.2.1
!
aaa new-model
!
aaa authentication login SERVER-AAA group RADIUS local 
aaa authentication login default local 
!
no ip cef
no ipv6 cef
!
username ISP password 0 LksNTB
username admin privilege 15 secret 5 $1$mERr$MmdodDsCCQflw.d5M6q3C.
!
license udi pid CISCO1941/K9 sn FTX15243J7P-
license boot module c1900 technology-package securityk9
!
crypto isakmp policy 46
encr aes 256
authentication pre-share
group 5
!
crypto isakmp key Mandalika address 192.0.2.18
!
crypto ipsec transform-set BRANCH-SET esp-aes esp-sha-hmac
!
crypto map BRANCH-MAP 46 ipsec-isakmp 
set peer 192.0.2.18
set transform-set BRANCH-SET 
match address INTERESTING-TRAFFIC
!
ip ssh version 2
ip domain-name branch.mandalika.id
ip name-server 192.0.2.1
!
spanning-tree mode pvst
!
interface Tunnel46
ip address 10.0.14.6 255.255.255.252
mtu 1476
tunnel source Serial0/0/1
tunnel destination 192.0.2.18
!
interface GigabitEthernet0/0
description ROUTER-ON-STICK
no ip address
ip nat inside
duplex auto
speed auto
!
interface GigabitEthernet0/0.1
encapsulation dot1Q 1 native
ip address 10.0.15.1 255.255.255.248
ip nat inside
!
interface GigabitEthernet0/0.2
description SALES
encapsulation dot1Q 2
ip address 10.0.15.17 255.255.255.240
ip nat inside
!
interface GigabitEthernet0/1
no ip address
duplex auto
speed auto
shutdown
!
interface Serial0/0/0
no ip address
clock rate 2000000
shutdown
!
interface Serial0/0/1
description TO ISP
ip address 192.0.2.26 255.255.255.248
encapsulation ppp
ppp authentication chap
ip nat outside
crypto map BRANCH-MAP
!
interface Vlan1
no ip address
shutdown
!
router ospf 46
log-adjacency-changes
network 10.0.14.4 0.0.0.3 area 0
network 10.0.15.0 0.0.0.7 area 0
network 10.0.15.16 0.0.0.15 area 0
default-information originate
!
ip nat inside source list ALLOW-INTERNET interface Serial0/0/1 overload
ip nat inside source static 10.0.15.4 192.0.2.27 
ip classless
ip route 0.0.0.0 0.0.0.0 192.0.2.25 
!
ip flow-export version 9
!
ip access-list standard ALLOW-INTERNET
permit 10.0.15.0 0.0.0.7
permit 10.0.15.16 0.0.0.15
ip access-list extended INTERESTING-TRAFFIC
permit gre host 192.0.2.26 host 192.0.2.18
permit ip 10.0.15.0 0.0.0.7 host 10.0.14.2
permit ip 10.0.11.0 0.0.0.255 host 10.0.15.4
permit ip 10.0.12.0 0.0.0.255 host 10.0.15.4
!
RADIUS server SERVER-AAA
address ipv4 10.0.15.4 auth-port 1645
key NTBJawara2021
RADIUS server 10.0.15.4
address ipv4 10.0.15.4 auth-port 1645
key NTBJawara2021
!
line con 0
login authentication SERVER-AAA
!
line aux 0
!
line vty 0 4
session-limit 5
login authentication SERVER-AAA
transport input ssh
!
ntp authentication-key 46 md5 080F786C211C071606595C567B 7
ntp trusted-key 46
ntp server 192.0.2.2 key 46
!
end


BRANCH#
```

### Completed Points: 8%

---

## Task 17
### VPN Connection Verification from the End Device at HQ MATARAM  

1. Use Simple PDU to verify the connection between the End Device on VLAN SALES and WLAN ENGINEER at BRANCH SUMBAWA and the End Device on VLAN HRD and MARKETING at HQ MATARAM. <br>
Check that the connection is working properly.
 
{{< bundle-image name="SimplePDU_HQMATARAM_SWBRANCH_END_DEVICE.PNG" alt="SimplePDU from VLAN SALES and WLAN ENGINEER to VLAN HRD And MARKETING">}}

2. Use a browser to verify access to HTTP and HTTPS services on the HQ MATARAM Server by entering the addresses http://10.0.14.2 and https://10.0.14.2 from the End Device on VLAN SALES and WLAN ENGINEER from BRANCH SUMBAWA.<br>
Check that HTTP and HTTPS services can be accessed. 

{{< bundle-image name="Access_BRANCH_SERVER_HTTP_HTTPS_FROM_VLAN_11_12.PNG" alt="HTTP and HTTPS Access from VLAN SALES and WLAN ENGINEER">}}

3. Access the FTP service on the BRANCH Server using the FTP Client by entering the address 10.0.15.4 from the End Device on the HRD and MARKETING VLAN of HQ MATARAM.<br>
For FTP authentication, the user is "lks" and the password is "ntb."<br>
Check that the FTP service can be accessed. 

{{< bundle-image name="Access_BRANCH_SERVER_FTP_FROM_VLAN_11_12.PNG" alt="FTP Access to BRANCH Server from VLAN HRD and MARKETING">}}


### Completed Points: 0%

<a href="#top">Back to top</a>

---

## Task 18
### VPN Connection Verification from the End Device at BRANCH SUMBAWA 

1. Verification the connection using Simple PDU from the End Device located on VLAN SALES and WLAN ENGINEER from BRANCH SUMBAWA to the End Device located on VLAN HRD and MARKETING at HQ MATARAM. <br>
Make sure the connection is successful. 

{{< bundle-image name="SimplePDU_SWBRANCH_HQMATARAM_END_DEVICE.PNG" alt="Simple PDU from VLAN SALES and WLAN ENGINEER to VLAN HRD and MARKETING">}}

2. Verification of access to HTTP and HTTPS services located at SERVER HQ MATARAM using a browser by accessing the addresses http://10.0.14.2 and https://10.0.14.2 from the End Device located on VLAN SALES and WLAN ENGINEER from BRANCH SUMBAWA. <br>
Make sure HTTP and HTTPS services can be accessed. 

{{< bundle-image name="Access_HQ_SERVER_HTTP_HTTPS_FROM_VLANSALES_ENGINEER.PNG" alt="HTTP and HTTPs Access to HQ MATARAM Server from VLAN SALES and WLAN ENGINEER">}}

3. Access the FTP service on the HQ Server using the FTP Client by entering the address 10.0.14.2 from the End Device on the SALES VLAN and WLAN ENGINEER of BRANCH SUMBAWA. <br>
The user for FTP authentication is "lks" with password "ntb". <br>
Make sure the FTP service can be accessed.  

{{< bundle-image name="Access_HQ_SERVER_FTP_FROM_VLANSALES_ENGINEER.PNG" alt="FTP Access to HQ Server from VLAN SALES and WLAN ENGINEER">}}

### Completed Points: 0%

<a href="#top">Back to top</a>

---

## Task 19
### Roled Based Access Control (RBAC) configuration at router HQ MATARAM

1. Create a view called "support" and set its password to "mandalika".

```HTML
HQ(config)#parser view support
HQ(config-view)#%PARSER-6-VIEW_CREATED: view 'support' successfully created.
HQ(config-view)#secret 5 mandalika
```

2. Configure the view to only run the "ping" and "traceroute" commands, as well as any commands that begin with "show".

```HTML
HQ(config-view)#commands exec include ping
HQ(config-view)#commands exec include traceroute
HQ(config-view)#commands exec include all show
```

3. Verify the configuration that has been done to ensure that it is following the requirements.

```HTML
parser view support
secret 5 $1$mERr$CuHT/qBH1YqIQvmMYg1Ev/
commands exec include ping
commands exec include all show
commands exec include traceroute
```

### Completed Points: 1%

<a href="#top">Back to top</a>

---

## Task 20 
### Roled Based Access Control (RBAC) configuration at router BRANCH SUMBAWA 

1. Create a view called "helpdesk" and set its password to "mandalika".
   
```HTML 
BRANCH(config)#parser view helpdesk
BRANCH(config-view)#%PARSER-6-VIEW_CREATED: view 'helpdesk' successfully created.
BRANCH(config-view)#secret 0 mandalika
```

2. Configure a view that can only run the "show ip protocols," "display ip route," "show ip interface short," "ping," and "traceroute" commands.

```HTML    
BRANCH(config-view)#commands exec include show ip protocols
BRANCH(config-view)#commands exec include show ip route
BRANCH(config-view)#commands exec include show ip interface brief
BRANCH(config-view)#commands exec include ping
BRANCH(config-view)#commands exec include traceroute
```

3. Verify the configuration that has been carried out to ensure that it is following the requirements.

```HTML
parser view helpdesk
secret 5 $1$mERr$CuHT/qBH1YqIQvmMYg1Ev/
commands exec include show
commands exec include show ip
commands exec include show ip interface
commands exec include show ip interface brief
commands exec include show ip protocols
commands exec include show ip route
commands exec include traceroute
```    

### Completed Points: 1%

<a href="#top">Back to top</a>

---

## Complete Configuration

{{< bundle-image name="FINISH.PNG" alt="TASK FINISHED!">}}

### Router HQ MATARAM

```HTML
HQ#show run
Building configuration...

Current configuration : 3971 bytes
!
version 15.1
no service timestamps log datetime msec
no service timestamps debug datetime msec
no service password-encryption
!
hostname HQ
!
enable secret 5 $1$mERr$ix/GaeUGI2eMcE7/bu9c2/
!
ip dhcp excluded-address 10.0.11.1
ip dhcp excluded-address 10.0.12.1
ip dhcp excluded-address 10.0.13.1 10.0.13.4
!
ip dhcp pool HRD
network 10.0.11.0 255.255.255.0
default-router 10.0.11.1
dns-server 192.0.2.1
ip dhcp pool MARKETING
network 10.0.12.0 255.255.255.0
default-router 10.0.12.1
dns-server 192.0.2.1
ip dhcp pool NATIVE
network 10.0.13.0 255.255.255.0
default-router 10.0.13.1
dns-server 192.0.2.1
!
aaa new-model
!
aaa authentication login SERVER-AAA group RADIUS local 
aaa authentication login default local 
!
no ip cef
no ipv6 cef
!
username ISP password 0 LksNTB
username admin privilege 15 secret 5 $1$mERr$ix/GaeUGI2eMcE7/bu9c2/
!
license udi pid CISCO1941/K9 sn FTX1524O1D2-
license boot module c1900 technology-package securityk9
!
crypto isakmp policy 46
encr aes 256
authentication pre-share
group 5
!
crypto isakmp key Mandalika address 192.0.2.26
!
crypto ipsec transform-set HQ-SET esp-aes esp-sha-hmac
!
crypto map HQ-MAP 46 ipsec-isakmp 
set peer 192.0.2.26
set transform-set HQ-SET 
match address INTERESTING-TRAFFIC
!
ip ssh version 2
ip domain-name mandalika.id
ip name-server 192.0.2.1
!
spanning-tree mode pvst
!
interface Tunnel46
ip address 10.0.14.5 255.255.255.252
mtu 1476
tunnel source Serial0/0/0
tunnel destination 192.0.2.26
!
interface GigabitEthernet0/0
description ROUTER-ON-STICK
no ip address
duplex auto
speed auto
!
interface GigabitEthernet0/0.11
description VLAN 11 HRD
encapsulation dot1Q 11
ip address 10.0.11.1 255.255.255.0
ip nat inside
!
interface GigabitEthernet0/0.12
description VLAN 12 MARKETING
encapsulation dot1Q 12
ip address 10.0.12.1 255.255.255.0
ip nat inside
!
interface GigabitEthernet0/0.13
description VLAN 13 NATIVE
encapsulation dot1Q 13 native
ip address 10.0.13.1 255.255.255.0
ip nat inside
!
interface GigabitEthernet0/1
description TO HQ SERVER
ip address 10.0.14.1 255.255.255.252
ip nat inside
duplex auto
speed auto
!
interface Serial0/0/0
description TO ISP
ip address 192.0.2.18 255.255.255.248
encapsulation ppp
ppp authentication chap
ip nat outside
crypto map HQ-MAP
!
interface Serial0/0/1
no ip address
clock rate 2000000
shutdown
!
interface Vlan1
no ip address
shutdown
!
router ospf 46
log-adjacency-changes
network 10.0.14.0 0.0.0.3 area 0
network 10.0.14.4 0.0.0.3 area 0
network 10.0.11.0 0.0.0.255 area 0
network 10.0.12.0 0.0.0.255 area 0
default-information originate
!
ip nat inside source list ALLOW-INTERNET interface Serial0/0/0 overload
ip nat inside source static 10.0.14.2 192.0.2.19 
ip classless
ip route 0.0.0.0 0.0.0.0 192.0.2.17 
!
ip flow-export version 9
!
ip access-list extended ALLOW-INTERNET
permit udp any host 192.0.2.1 eq domain
permit tcp 10.0.11.0 0.0.0.255 host 192.0.2.5 eq smtp
permit tcp 10.0.11.0 0.0.0.255 host 192.0.2.5 eq pop3
permit tcp 10.0.12.0 0.0.0.255 any
permit tcp 10.0.13.0 0.0.0.255 any
ip access-list extended INTERESTING-TRAFFIC
permit gre host 192.0.2.18 host 192.0.2.26
permit ip 10.0.15.0 0.0.0.7 host 10.0.14.2
permit ip 10.0.11.0 0.0.0.255 host 10.0.15.4
permit ip 10.0.12.0 0.0.0.255 host 10.0.15.4
!
no cdp run
!
RADIUS server SERVER-AAA
address ipv4 10.0.14.2 auth-port 1645
key NTBJawara2021
RADIUS server 10.0.14.2
address ipv4 10.0.14.2 auth-port 1645
key NTBJawara2021
!
line con 0
login authentication SERVER-AAA
!
line aux 0
!
line vty 0 4
session-limit 5
login authentication SERVER-AAA
transport input ssh
!
parser view support
secret 5 $1$mERr$CuHT/qBH1YqIQvmMYg1Ev/
commands exec include ping
commands exec include all show
commands exec include traceroute
!
ntp authentication-key 46 md5 080F786C211C071606595C567B 7
ntp trusted-key 46
ntp server 192.0.2.2 key 46
!
end


HQ#
```

### Router BRANCH SUMBAWA

```HTML
BRANCH#sh run
Building configuration...

Current configuration : 3484 bytes
!
version 15.1
no service timestamps log datetime msec
no service timestamps debug datetime msec
no service password-encryption
!
hostname BRANCH
!
enable secret 5 $1$mERr$ix/GaeUGI2eMcE7/bu9c2/
!
ip dhcp excluded-address 10.0.15.17
!
ip dhcp pool SALES
network 10.0.15.16 255.255.255.240
default-router 10.0.15.17
dns-server 192.0.2.1
!
aaa new-model
!
aaa authentication login SERVER-AAA group RADIUS local 
aaa authentication login default local 
!
no ip cef
no ipv6 cef
!
username ISP password 0 LksNTB
username admin privilege 15 secret 5 $1$mERr$MmdodDsCCQflw.d5M6q3C.
!
license udi pid CISCO1941/K9 sn FTX15243J7P-
license boot module c1900 technology-package securityk9
!
crypto isakmp policy 46
encr aes 256
authentication pre-share
group 5
!
crypto isakmp key Mandalika address 192.0.2.18
!
crypto ipsec transform-set BRANCH-SET esp-aes esp-sha-hmac
!
crypto map BRANCH-MAP 46 ipsec-isakmp 
set peer 192.0.2.18
set transform-set BRANCH-SET 
match address INTERESTING-TRAFFIC
!
ip ssh version 2
ip domain-name branch.mandalika.id
ip name-server 192.0.2.1
!
spanning-tree mode pvst
!
interface Tunnel46
ip address 10.0.14.6 255.255.255.252
mtu 1476
tunnel source Serial0/0/1
tunnel destination 192.0.2.18
!
interface GigabitEthernet0/0
description ROUTER-ON-STICK
no ip address
ip nat inside
duplex auto
speed auto
!
interface GigabitEthernet0/0.1
encapsulation dot1Q 1 native
ip address 10.0.15.1 255.255.255.248
ip nat inside
!
interface GigabitEthernet0/0.2
description SALES
encapsulation dot1Q 2
ip address 10.0.15.17 255.255.255.240
ip nat inside
!
interface GigabitEthernet0/1
no ip address
duplex auto
speed auto
shutdown
!
interface Serial0/0/0
no ip address
clock rate 2000000
shutdown
!
interface Serial0/0/1
description TO ISP
ip address 192.0.2.26 255.255.255.248
encapsulation ppp
ppp authentication chap
ip nat outside
crypto map BRANCH-MAP
!
interface Vlan1
no ip address
shutdown
!
router ospf 46
log-adjacency-changes
network 10.0.14.4 0.0.0.3 area 0
network 10.0.15.0 0.0.0.7 area 0
network 10.0.15.16 0.0.0.15 area 0
default-information originate
!
ip nat inside source list ALLOW-INTERNET interface Serial0/0/1 overload
ip nat inside source static 10.0.15.4 192.0.2.27 
ip classless
ip route 0.0.0.0 0.0.0.0 192.0.2.25 
!
ip flow-export version 9
!
ip access-list standard ALLOW-INTERNET
permit 10.0.15.0 0.0.0.7
permit 10.0.15.16 0.0.0.15
ip access-list extended INTERESTING-TRAFFIC
permit gre host 192.0.2.26 host 192.0.2.18
permit ip 10.0.15.0 0.0.0.7 host 10.0.14.2
permit ip 10.0.11.0 0.0.0.255 host 10.0.15.4
permit ip 10.0.12.0 0.0.0.255 host 10.0.15.4
!
RADIUS server SERVER-AAA
address ipv4 10.0.15.4 auth-port 1645
key NTBJawara2021
RADIUS server 10.0.15.4
address ipv4 10.0.15.4 auth-port 1645
key NTBJawara2021
!
line con 0
login authentication SERVER-AAA
!
line aux 0
!
line vty 0 4
session-limit 5
login authentication SERVER-AAA
transport input ssh
!
parser view helpdesk
secret 5 $1$mERr$CuHT/qBH1YqIQvmMYg1Ev/
commands exec include ping
commands exec include show
commands exec include show ip
commands exec include show ip interface
commands exec include show ip interface brief
commands exec include show ip protocols
commands exec include show ip route
commands exec include traceroute
!
ntp authentication-key 46 md5 080F786C211C071606595C567B 7
ntp trusted-key 46
ntp server 192.0.2.2 key 46
!
end


BRANCH#
```

## Summary
It was enjoyable for me, specifically the VPN section, because I rarely use it in my daily work.

Unfortunately, I ran into issues where the "WLAN ENGINEER" section could not be pinged from the client at HQ MATARAM, while the "WLAN ENGINEER" could ping towards the HQ MATARAM client.

My guess is in the OSPF section.

Finally, I was unable to complete the task completely, either due to a Packet Tracer problem or an incorrect setting.


Please let me know if you spot any setting problems or typos.
Thank you very much. 


<!-- 
^                                                          .~J5?^:....^7YJ~.......^5J~5?:.::::::::::::::^::::^^^::::^^^::::^^^:::^^^::::::^:::::^^::::^^:~!J5PY??J??7~~!7!~~^.             .:^^^..        :~!^. ..::..  ..^~~:.                    .::.                                                                                   
^                                                        .!Y5Y!:........^?5?^:...:?5~:7P^:.::::::::::::::::^^^:::::^^^::::^^^:::^^^:::::^^^::::^^:::::^::..:^?Y^.::^!7?7777?JJ?7~:        .:^:.            .^~~~~~~~~~~~~~^:.                   .^7Y55YJ7^                                                                                
^                                                      .!YPJ~:...........:7YYYJ7~^YY:.^57:.:::::::::::::::^^^:::::^^^::::^^^:::^^::::::^^::::^^^:::::^:::::::^Y?:............:~7?J?^~!!~.                     .....  .....                     ^JPP5YYY5PPJ:                                                                              
^                                                    :7Y5J~.................::~7JJY?:.:7?:.::::::::::::::^^::::::^^::::^^^^:::::::::::^^::::^^::::::::::::::::!Y!................:^~~~!JY!.                                                  ^JP5YYYYY55Y5P5~                                                                             
^                                         :~!!!~~~^^755?^........................^75~..^Y7::::::::::::::^^:::::^^^::::^^^^:::::::::::::::::^^:::::::::::::::::^5Y:................... ..!5!                                                .7PPYYYYYYY5P5YYPP~                                                                            
^                                       .!5YY5YYY5PP5?^............................~7:.:!5~.:::::::::::^:::::::^^::::^^^:::::::::::::::::^^^:::::::::::::::::.:?5~.......................!J~                                             .^YP5YYYYYYYYYP5YYY55~                                                                           
^                                       .!5YYYYY55Y7:...................................:?J^.:::::::::::::::::::::::^^^::::::::::::::::::^::::::::::::::......:!Y^.......................:75J~                                          .7PPYYYYYYYYYY5P5YYYYP5^                                                                          
^                                       .7PYYY5PP7:......................................:J?:.::::::::::::::::::::::^::::::::::::::::::::::::::^!J?7!~~~^^~~^^^~57.........................^?5J~.:::^^~~~^^~~~!~^:^^^^^.....          .~J55YYYYYYYYYY5GPYYYYY5GY~^~~~^:.                                                                  
^                                       .7PY5P5?^........................................^Y?:...::::::::::::::::::::::::::::::::::::::::::::::::^~~!!7?JJY555P55P5~..........................^?555555555555555YYYY55555YYYYYYJ7~^:.  .755YYYYYYYYYYYPP5YYYYYYY5G5555555YJ?~.                                                              
^                                       .!P55?~................................. ........!PY~.....::::::::::::::::::::::::::::::::::::::::::::::::......::::^!7?J5Y^...........................:75PYJJJJJJJJJJJJJJJJJJJJJJJJYYY555J?7?5P5555P55P555PP5YYYYYYY??P5JJJJJJJY5P57^.                                                           
^                                       :?P?^..................................:~^^^^^~!?Y555J~:.....::::::::::::::::::::::::::::::::::::::::::::::::::..........^?Y^............................:755YJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJYYJYYYYYYYYYYPP5YYYYYYYJ!^~JPYJJJJJJJJJY55Y7:                                                         
^                                     .^JY!:.................................:!J5Y5YY555YJJJY5PY^........::::::::::::::::::::::::::::::::::::::::::...............^YP!.............................:7Y5YJJJJJJJJJJJJJJJJJJJJJJJJJJJJYYY5555YYYYYYPPYYYYYYY?~^^^!55JJJJJJJJJJJJY5P?^                                                       
^                                    :7Y!:..................................^JPYJJJJJJJJJJJJJJJ55?^............:::::::::::::::::::::::::::::::::::.................^JPJ:.............................:7Y5YJJJJJJJJJYJYYY55555PPPPP555555YYYYYYYYYP5YYYYYJ!^^^^^^~JPYJJJJJJJJJJJJYP57.                                                     
^                                  .!Y5!..................................~JY5JJJJJJJJJJJJJJJJJJYPP?~:...............::::::::::::::::::::::::........................~Y5!. ............................^JPYYYY5555555YY5PP5YYYYYYYYYYYYYYYYYYYYY5P5YYYY7~^^^^^^^^~YPYJJJJJJJJJJJJJ5PY^                                                    
^                                 :JPJ^.................................:7555YJJJJJJJJJJJJJJJJJYYYYY5Y?!^:............................................................:75Y~..............................~JPP5YJ??7!7?YP5YYYYYYYYYYYYYYYYYYYYYYY5G5YY?~^^^^^^^^^~?Y5P5JJJJJJJJJJJJJYP5~                                                   
^                                :J5!:................................:~J5J?Y55YJJJJJJJJJJJJJJJJJJJJJJY55YYJ?!~~^^^::::::::::^^^^^~~~!77!7???7!!~~~~^^^::...............:7YY!: ...........................:!YY?777777JPP55YYYYYYYYYYYYYYYYYYYYYYPPYJ!^^^^^^^^^~?YYYY5PYJJJJJJJJJJJJJJY5!.                                                 
^                          :!777?557:................................^JPY?7777?JYYYJJJJJJJJJJJJJJJJJJJJJJJYY555555555YJYYY55YY55YY5555Y5YYYY55555555555YYJJ??7!~^^^^::.....~Y5!.............................:75Y777777?J5P5YYYYYYYYYYYYYYYYYYYY5G57^^^^^^^^^~?YYYYYYYPPJJJJJJJJJJJJJJJYP?.                                                
^                        .7PJ~^^!???YJ7^............................~YJ?777777777?YPYJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJYY?JJJJJJJJJJJJJJJJJJJJJJJJJJJJJYYYYYYYY55P55YYYJYYY5P7..............................^J5?7777?5P5YYYYYYYYYYYYYYYYYYYYY5G?^^^^^^^^~7JYYYYYYY5P5JJJJJJJJJJJJJJJJYPY:                                               
^                       :J5!.       .^!77!^.......................^?J?7777777777?Y5J?JJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJYY?JJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJY5!...............................!YJ777YP55YYYYYYYYYYYYYYYYYYYYYP5!~!~^^^~7JYYYYYYYY5PPJJJJJJJJJJJJJJJJJJJ5Y^                                              
^                      :?5!:...         .^7J7^...................~JJ7777777777?JYJ??JJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJ7JJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJY57...............................^J5?777?YP5YYYYYYYYYYYYYYYYYYYPJY5PY~~7JYYYYYYYY5PPYJJJJJJJJJJJJJJJJJJJJJP5~                                             
^                     .?Y!^^^^^::.        .^7YJ~:...............!J?77777777?YY7!7?JJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJ7!?JJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJYP?:...............................7PY77?5PYYYYYYYYYYYYYYYYYYY5PY7^~5YJYYYYY55PPP5YJJJJJJJJJJJJJJJJJJJJJJJJ5P~                                            
^                    .7Y!^^^~^^~^^::.        :~JJ~............^?Y?7777777?YPY!~?JJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJ7~?JJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJYPY:...............................~YPJYG5YYYYYYYYYYYYYYYYYYYGY:  .!PYY555PPP5YJJJJJJJJJJJJJJJJJJJJJJJJJJJJ55~                                           
^                   .?5^..::^^^^~~~~^::.       .~YY~........:!YY?777777?Y5J!!7JJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJ?~?JJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJ5Y:...............................:JPYY555YYYYYYYYYYYYYYYY5G!.   :YP5PPP5555555555YY55YYYYYJJJJJJJJJJJJJJJ5P!                                          
^               :~~~7PJ^:.    .::^^~~~^^::.      :7Y7^.....:?PJ777777?JJ7~!?JJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJY!!JJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJ5J:............................. .:7P5YPPYYYYYYYYYYYYYYY5GY:    .!P5555555555555555555555555555YJJJJJJJJJJ5P~                                         
^            .~JJJ?777?JJJJ?7~:   ..:^^~^^^^:.     :!?!:..^YPJ77777?YY7~7?JJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJY7^?JJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJYJ^...........................:~7JJ5PPYJJ55YYYYYYYYYYYYP5~.    .7P5YYYYYYYYYYYYYYYYYYYYYYYYYYY5P5JJJJJJJJJ55!                                        
^          .!Y5?~^^^^^^^^~~!?YYJ?~.  ..^^~~~~^:.     :JJ:^YPJ7777?Y5?!!?JJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJ5J^7JJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJ5Y^......................:^!7??7~::^:. .:?55YYYYYYYYYPP!.     :YPYYYYYYYYYYYYYYYYYYYYYYYYYYYYY5PYJJJJJJJJJYP?:                                      
^         .?GY!^^^^^^^^^^^^^^^~7JYYJ~.  .:^^~^^^:.    :7YJP5J??JYY?!!?JJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJ55~~JJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJYJ^..................:~7J?~:..       ..:^!YPYYYYYYYPG7.     .7P?7?JJYYYYYYYYYYYYYYYYYYYYYYYYY5PYJJJJJJJJJJYGY:                                     
^        .7PJ~^^^^^^^^^^^^^^^^^^^~!?YJ!.   .^^~^^^:.   .~Y5555PGY~:~JJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJYP!:7JJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJY?:...............^7JY7^.       ..:^^^~^^!?55YYYYPG7.     .?PJ~^^^~~!77??JJYYYYYYYYYYYYYYYYY5PYJJJJJJJJJJJYPJ.                                    
^       .JP?~^^^^^^^^^^^^^^^^^^^^^^^^~7?~.  .:^^^^^^:.   :YPYYJ!:.:7JJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJYP?:~??JJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJYJ:............~JJ?~.       ..:^^~~~~^^:::~Y5YYPGJ:    .~YP?~^^^^^^^^^^^^^~~~!!77??JJJYYYYYPPJJJJJJJJJJJJJYP?:                                   
^      :JP7~^^^^^^^^^^^^^^^^^^^^^^^^^^^~77:   .:^^^^^^:..7PJ!~:..^?JJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJYPJ^:!^:!JJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJ5Y^.........^75J^.       .^^^~~~^^::..    ^J5P5!. .:~?YPY7~^^^^^^^^^^^^^^^^^^^^^^^^^~~~~!JPYJJJJJJJJJJJJJJJ5?.                                  
^     .757~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^!J?:   .:^^^^^~Y57^::.:!JJJ?JJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJYP5~:::..^JJJ??JJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJ5Y^......:!?7~.      .:^^~^^^^:..         ^5PJ777J5P55YYYYJJ?7!~^^^^^^^^^^^^^^^^^^^^^^^755JJJJJJJJJJJJJJJJJ5?.                                 
^     ^Y?~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^~J5~   .:^^~7YJ~!~::!?JJ7^!JJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJ5G7:.:.::7JJ7:7JJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJ55^....^JJ^       .:^~~~^^:..     .:^^~!!?5P55PP55YYYYYYYYYYYYYJ?7!~^^^^^^^^^^^^^^^^~JP5JJJJJJJJJJJJJJJJJJYPJ:                                
^    .7Y!^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^~?J7~:  .~?J7!?JJ?JJJJJ!!JJJJJJJJJJJJJJJJY5YJJJJJ?!7JJJJJJJJJJJJJYPY^^!::.^JJJ^:?JJJJJJJJJJJJJJJJJJJJJJJJJJJJJY5JJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJ55^.~?Y?:.     .:^^^^^^:.    .^!7YYYJ?77!!!~!!7?Y55YYYYYYYYYYYYYYYYJJ?7!~^^^^^^^^^~YP5JJJJJJJJJJJJJJJJJJJJ5GJ:                               
^    :??~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^~!777!!?!!?JJJJJJJJJJJJJJJJJJJJJJJJJJJ55YJJJJJ7^^?JJJJJJJJJJJJJP5~^7J?!~7JJJ??JJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJ5YJJJJJJJJJJJJJJ?~7JJJJJJJJJJJJJJJJYPY~J5!.     .:^^^^^:..   :!JYYY?7~~^^^^^^^^^^^^~7Y55YYYYYYYYYYYYYYYYYYYYJ?7!~^^^!5PYJJJJJJJJJJJJJJJJJJJJJJ5GJ.                              
^    :J7~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^!Y5?7?JJJJJJJJJJJJJJJJJJJJJJJJJJJJ55JJJJJJ!::?JJJJJJJJJJJJJ5G7:!JJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJ5PYJJJJJJJJJJJJJJ~:7JJJJJJJJJJJJJJJJYP5Y^.    .:^^^^^:.    :7JJ?!~^^^^^^^^^^^^^^^^^^^~7Y5555YYYYYYYYYYYYYYYYYYYYYJ??5PYJJJJJJJJJJJJJJJJJJJJJJJJ5P7.                             
^    ^Y7~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^~?5J??JJJJJJJJJJJJJJJJJJJJJJJJJJJJYPPJJJJJJJ?!?JJJJJJJJJJJJJYPY^^?JJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJP5JJJJJJJJJJJJJJ?^~JJJJJJJYYJJJJJJJJ55~.    .^^^^^:.    :!?!~^^^^^^^^^^^^^^^^^^^^^^^^^~7YPPP555YYYYYYYYYYYYYYYYYY5PPYJJJJJJJJJJJJJJJJJJJJJJJJJJ5P!.                            
^   .~J!^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^!?55??JJJJJJJJJJJJJJJJJJJJJJJJJJJJJYP5JJJJJJJJJJJJJJJJJJJJJJJJ5G7:7JJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJPPYJJJJJJJJJJJJJJJJJJJJJJJJJ55JJJJJY5!.    :^~^^^.    .~?!~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^~?5P55P555YYYYYYYYYYYYYYYPGYJJJJJJJJJJJJJJJJJJJJJJJJJJJJPP~                            
^   .7J~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^~?P5YJJJY55JJJJJJJJJJJJJJJJJJJJJJJJJ5P5JJJJJJJJJJJJJJJJJJJJJJJJJP5^!JJJJJJJJJJJJJJJJJJJJJJJJYYJJJJJJJJJJJJJJJJJJJ5PPYJJJJJJJJJJJJJJJJJJJJJJJJJY55JJYGY:   .:^^^^^::^~!?YY!~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^~7Y5YY555P555YYYYYYYYY5PPYJJJJJJJJJJJJJJJJJJJJJJJJJJJJJYP5:                           
^   .7Y~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^~7Y55YJJJJ5P5JJJJJJJJJJJJJJJJJJJJJJJJJ5P5JJJJJJJJJJJJJJJJJJJJJJJJJYP!^?JJJJJJJJJJJJJJJJJJJJJJJJ5YJJJJJJJJJJJJJJJJJJJ5PP5JJJJJJJJJJJJJJJJJJJJJJJJJJJ5PJYGJ.  .:~!??JJYYJJY5?~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^!JPYYYYY555PP55555PP55YJJJJJJJJJJJJJYJJJJJJJJJJJJJJJJJJP7.                          
^   :JJ~^^^^^^^^^^^^^^^^^^^^^^^^^^^^~!?5P5JJJJJYPPYJJJJJJJJJJJJJJJJJJJJJJJJJ5G5JJJJJJJJJJJJJJJJJJJJJJJJJJ55^7JJJJJJJJJJJJJJJJJJJJJJJJYPJJJJJJJJJJJJJJJJJJJJYP555JJJJJJJJJJJJJJJJJJJJJJJJJJJ55YPP~ .~?JJJ77!~~^^~~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^!YY?JYYYYYY55555PPYJJJJJJJJJJJJJJJJY5JJJJJJJJJJJJJJJJJYY~                          
^   :YJ~^^^^^^^^^^^^^^^^^^^^^^^^^~!J555YJJJJJJ5P5JJJJJJJJJJJJJJJJJJJJJJJJJJ5PYJJJJJJJJJJJJJJJJJJJJJJJJJJYPJ!?JJJJJJJJJJJJJJJJJJJJJJJJ55JJJJJJJJJJJJJJJJJJJJYPYJPYJJJJJJJJJJJJJJJJJJJJJJJJJJJ55YPJ~?J7~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^~?P!!7JYYYYYYYYY55PYJJJJJJJJJJJJJJJJYJJJJJJJJJJJJJJJJJJ5Y^                         
^   :Y?~^^^^^^^^^^^^^^^^^^^^^~!7JY55YJJJJJJJY55YJJJJJJJJJJJJJJJJJJJJJJJJJJ5G5JJJJJJJJJJJJJJJJJJJJJJJJJJ5PPJJYYYYYJJJJJJJJJJJJJJJJJJJY5YJJJJJJJJJJJJJJJJJJJJJP5!?PYJJJJJJJJJJJJJJJJJJJJJJJJJJJ55YP5J~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^7P?^^~7JYYYYYYYYY5PPYJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJYPJ.                        
^   :Y?~^^^^^^^^^^^~~~~~!!7?Y555YJJJJJJJJJJ5P5JJJJJJJJJJJJJJJJJJJJJJJJJJJ5G5JJJJJJJJJJJJJJJJJJJJYY5555PP5PYYYYYY5YJJJJJJJJJJJJJJJJJJ55YJJJJJJJJJJJJJJJJJJYYYPP7!YPYJJJJJJJJJJJJJJJJJJJJJJJJJJJ5557~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^~?J~^^^~7JYYYYYYYYY555YJJJJJJJJJJJJJJYJJJJJJJJJJJJJJJJJYP7.                       
^   ^5?~^^^^^^^^^^~?YYYY555YYJJJJJJJJJJJJJ5PYJJJJJJJJJJJJJJJJJJJJJJJJJJJYG5JJJJJJJJJJJJJJJJJJY5555YY5P57JPJ?JJJJJJJJJJJJJJJJJJJJJJJJPPJJJJJJJJJJJJJJJY5555Y5PPJ77YPP5Y5555YYYJJJJJJJJJJJJJJJJJYGY~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^!57^^^^^~7JYYYYYYYYY5PPYJJJJJJJJJJJJ5YJJJJJJJJJJJJJJJJJ5P~                       
^  .~57~^^^^^^^^^^~7YYYYJJJJJJJJJJJJJJJJYPPJJJJJJJJJJJJJJJJJJJJJJJJJJJJYGPJJJJJJJJJJJJJJJJJJJYYJJJJ5PJ^:JPJJJJJJJJJJJJJJJJJJJJJJJJJYP5JJJJJJJJJJJJJJJJJJJJJYP5~..^JP5JJJJYYY555YJJJJJJJJJJJJJJYP?~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^~YJ^^^^^^^~7JYYYYYYYYY5P5YJJJJJJJJJJYPJJJJJJJJJJJJJJJJJJP5^                      
^   ~57~^^^^^^^^^^^^^~7?JY555555YYYY555PP5JJJJJJJJJJJJJJJJJJJJJJJJJJJJJPGYJJJJJJJJJJJJJJJJJJJJJJJ5P5!:.:YGJJJJJJJJYYJJJJJJJJJJJJJJJYP5JJJJJJJJJJJJJJJJJJJJJYPJ:...:7PPYJJJJJJJJJJJJJJJJJJJJJJJPJ~~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^~?57^^^^^^^^~7JYYYYYYYYY5P5YJJJJJJJJYPYJJJJJJJJJJJJJJJJJJPY:                     
^   ^Y7~^^^^^^^^^^^^^^^^^~!77???????77JP5JJJJJJJJJJJJJJJJJJJJJJJJJJJJJ5G5JJJJJJJJJJJJJJJJJJJJJJ5GP?^...:JPJJJJJJJJ55JJJJJJJJJJJJJJJJP5JJJJJJJJJJJJJJJJJJJJJ5P!......!PPYJJJJJJJJJJJJJJJJJJJJJYP?~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^!55?~^^^^^^^^~!JYYYYYYYYY5P5YJJJJJJJ55JJJJJJJJJJJJJJJJJJYG7.                    
^   ^J!^^^^^^^^^^^^^^^^^^^^^^^^^^^^^~7P5JJJJJJJJJJJJJJJJJJJJJJJJJJJJJYPPJJJJJJJJJJJJJJJJJJJJJ5PPJ~.....:JPYJJJJJJYPYJJJJJJJJJJJJJJJJP5JJJJJJJJJJJJJJJJJJJJYP?:.......~YP5JJJJJJJJJJJJJJJJJJJJYP7~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^~YPYY?~^^^^^^^^^!?YYYYYYYYY5P5YJJJJJYPYJJJJJJJJJJJJJJJJJJ55~                    
^  .^Y7~^^^^^^^^^^^^^^^^^^^^^^^^^^^~!55JJJJJJJJJJJJJJJJJJJJJJJJJJJJJJ5G5JJJJJJJJJJJJJJJJJJJYPPJ~.......:?PYJJJJJJYP5JJJJJJJJJJJJJJJJ5YJJJJJJJJJJJJJJJJJJJJPY^.........:755YJJJJJJJJJJJJJJJJJJYP7^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^~?PYYYY7~^^^^^^^^^!?YYYYYYYYY5PYJJJJJP5JJJJJJJJJJJJJJJJJJYPJ.                   
^   ^Y7~^^^^^^^^^^^^^^^^^^^^^^^^^^^!YPJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJPPYJJJJJJJJJJJJJJJJJJ5PJ~..........!P5JJJJJJJ5PJJJJJJJJJJJJJJJJ5YJJJJJJJJJJJJJJJJJJJYP!............^JP5JJJJJJJJJJJJJJJJJYPJ~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^~7P5YYYYY7~^^^^^^^^^!?YYYYYYY5P5JJJJJ55JJJJJJJJJJJJJJJJJJJY5~                   
^   ~Y7^^^^^^^^^^^^^^^^^^^^^^^^^^^~?PYJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJYP5JJJJJJJJJJJJJJJJJ5P5!............^5YJJJJJJJPPYJJJJJJJJJJJJJJJP5JJJJJJJJJJJJJJJJJJJP?:.............:75PYJJJJJJJJJJJJJJJJ55~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^!Y5YYYYYYJ7^^^^^^^^^^!?Y55555YJJJJJJ55JJJJJJJJJJJJJJJJJJJJP5^                  
^   ^Y?~^^^^^^^^^^^^^^^^^^^^^^^^^~!55JJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJ5PYJJJJJJJJJJJJJJJYP5!:.............:J5JJJJJJJPPYJJJJJJJJJJJJJJJ5PJJJJJJJJJJJJJJJJJJ5Y^................~JP5JJJJJJJJJJJJJJJYP7^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^~JPYYYYYYYYJ!^^^^^^^~7J555YJJJJJJJJJ55JJJJJJJJJJJJJJJJJJJJYG?.                 
^   ^Y?~^^^^^^^^^^^^^^^^^^^^^^^^^~YPJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJPPJJJJJJJJJJJJJJY557:...............:?GYJJJJJYJJ5JJJJJJJJJJJJJJJ5PJJJJJJJJJJJJJJJJJPP!...................~J55YJJJJJJJJJJJJJPJ~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^~?PYYYYYYYYYY?~^^^^!5PYJJJJJJJJJJJJJPYJJJJJJJJJJJJJJJJJJJJJP5^                 
^   ~PJ~^^^^^^^^^^^^^^^^^^^^^^^^~7P5JJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJYPYJJJJJJJJJJJJJ5PJ^..................!P5JJJJJYJ75YJJJJJJJJJJJJJJ5PJJJJJJJJJJJJJJJJ5P!.....................:~JP5YJJJJJJJJJJJ55!^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^~7P5YYYYYYYYYYY7~^?PYJJJJJJJJJJJJJJYPYJJJJJJJJJJJJJJJJJJJJJYP7.                
^   ^5J~^^^^^^^^^^^^^^^^^^^^^^^^~JPJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJ5PJJJJJJJJJJJJYPY~:...................:JPJJJJJ5Y!YPJJJJJJJJJJJJJJ5PYJJJJJJJJJJJJJJYP7:........................^?55YJJJJJJJJJJPJ~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^!5P5YYYYYYYYYYYY55YJJJJJJJJJJJJJJJYPYJJJJJJJJJJJJJJJJJJJJJJ5J:                
^   :JY~^^^^^^^^^^^^^^^^^^^^^^^^!55JJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJPPJJJJJJJJJJYP57:......................~55JJJJ5Y^!P5JJJJJJJJJJJJJYPYJJJJJJJJJJJJJYP7:...........................^?5P5YJJJJJJJ5P!^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^~YP5P55YYYYYYY5G5JJJJJJJJJJJJJJJJJ5PJJJJJJJJJJJJJJJJJJJJJJJYP!.               
^   .?5!^^^^^^^^^^^^^^^^^^^^^^^~7PYJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJP5JJJJJJJJJ5PJ~.........................75JJJJ5Y^:7P5JJJJJJJJJJJJYP5JJJJJJJJJJJJYP7...............................:!J5P5YJJJJJ5Y~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^~JPYYPPYYYYY5PPYJJJJJJJJJJJJJJJJJYP5JJJJJJJJJJJJJJJJJJJJJJJJP?.               
^   .75!^^^^^^^^^^^^^^^^^^^^^^^~7PYJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJYP5JJJJJJJJ5P?:..........................^Y5JJJYY^.:7PYJJJJJJJJJJJJPPJJJJJJJJJJJYP?:..................................^7YPP5YJJYP7~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^~J5~:~J5555PPPYJJJJJJJJJJJJJJJJJJ5GYJJJJJJJJJJJJJJJJY5JJJJJJ55~               
^   .7G7^^^^^^^^^^^^^^^^^^^^^^^~7PYJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJYP5JJJJJJJ5Y!.............................!P5JJYJ:..:75YJJJJJJJJJJJYPYJJJJJJJJJ5P?:......................................^7YP5YJ5Y~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^~?Y^   :!77!~5PJJJJJJJJJJJJJJJJJJPPJJJJJJJJJJJJJJJJJJPYJJJJJYG?.              
^   .!GJ~^^^^^^^^^^^^^^^^^^^^^^^!55JJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJYG5JJJJJJ5J^...........................::::?PYJY5^...:?PYJJJJJJJJJJYPYJJJJJJJJ5P?:..........:::^~!!!:.......................^7Y5PP7^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^~7P!.        ~55JJJJJJJJJJJJJJJJ5GYJJJJJJJJJJJJJJJJJJP5JJJJJJP5^              
^    ^55~^^^^^^^^^^^^^^^^^^^^^^~~?PYJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJYGYJJJJJ5J^...........................~JJ?775PYYG7. ..:7P5JJJJJJJJJJ55JJJJJJJ5P7:.....:^^~!!77!!7777^..........................^7Y5!^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^~7P?.        .^Y5JJJJJJJJJJJJJJYP5JJJJJJJJJJJJJJJJJJYPYJJJJJJ5G?.             
^    .?5!^^^^^^^^^^^^^^^^^^^^^^^^!Y5JJJJJJJJJJJJJJJJJJJJJJJJJJJJJJYPYJJJJ5Y^..................................^JP55Y?!^...~Y5YJJJJJJJJ5PYJJJJJ557:...^7JJ7~:::.... .  .....................^~:.....?P7^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^!57.          ^Y5JJJJJJJJJJJJJ5PJJJJJJJJJJJJJJJJJJJ5PYJJJJJJYPY:             
^    .~57~^^^^^^^^^^^^^^^^^^^^^^^~7PYJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJPYJJJ5P!:......^7!:.. ......................:?5P5~!J?:..:755JJJJJJJJPPJJJJPY^....:7?~:.............................:^~7J7^.....75?~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^!P7.           :?5YJJJJJJJJJJPPYJJJJJJJJJJJJJJJJJJJP5JJJJJJJJ5P!.            
^    .~PY~^^^^^^^^^^^^^^^^^^^^^^^^~YPYJJJJJJJYPYJJJJJJJJJJJJJJJJJJJ55JJYP7:........:7Y?7~^^:::^^^^^:..... .......^?PJ:.:.....^?5YJJJJJJYPYJYPY^....... ........::^^~7??JJ?JYY55YYYYYYY555Y?^:....~5?~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^!57.            .!55JJJJJJJJ5GYJJJJJJJJJJJJJJJJJJJYYJ5JJJJJJJYGJ.            
^     :YP!^^^^^^^^^^^^^^^^^^^^^^^^^!5PJJJJJJJ5GPJJJJJJJJJJJJJJJJJJJ5PJJP5^......:^~7?Y5PPPP55PP55YYY5YYJ?7~^^:... .^~:.........~J5YJJJJJ5PYPY^.........::^~!7JY555PP5?557!!7YP5YPPPPPPPPPPPP5YJ?!JY~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^!PJ.              ^JP5YJJJYPPYJJJJJJJJJJJJJJJJJJJYP~:7PYJJJJJJPY:            
^     .7P?~^^^^^^^^^^^^^^^^^^^^^^~^~75PYJJJJJYPP5JJJJJJJJJJJJJJJJJJYPYYGJ:.:^!?Y5PPPPPPPPP5J5PY?7!!?P5YPPPPP55YJ?!!~.............~J5YJJJYPPJ^......:~?JY5PPPPGPPPPPP5Y55YYY55P55555PPPPPPPPPPPPPPP57^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^7PY:               .~?55YYPPJJJJJJJJJJJJJJJJJJJJYPJ: .?PYJJJJJP5:            
^      :YY!^^^^^^^^^^^^^^^^^^^^^^^^^^!5PYJJJJYPJYPYJJJJJJJJJJJJJJJJYP5Y5~..^YY5PPPPPPPPPPP5555555555555PPPPPPPGPPPPPYJ7^...........~?5YJJ5G7........^JPPPPP55YYJJ??77!~^^^^^^^^^:::^^~~!7?Y55PPPPPPJ~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^~7GJ.                  .~YP5JJJJJJJJJJJJJJJJJJJJYP?:   :?PYJJJJP5:            
^      .7P7~^^^^^^^^^^^^^^^^^^^^^^^^^^!YPYJJJYPJ!JPYJJJJJJJJJJJJJJJJ5P55~..~Y5PP55J7!!~^^^:::::::::::^^^~~!!77?JJY55PPY!.............^7JYY557......^?Y?!~~^::..............................::~7JY5PP?~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^~757.                 .~JPYJJJJJJJJJJJJJJJJJJJJYPY:     :JPJJJJPJ:            
^       ^57~^^^^^^^^^^^^^^^^^^^^^^^^^^^~JPYJJJP5!~?PPYJJJJJJJJJJJJJJYPP5~..^7?7~^:................................::~!?7^..............:~?JYPJ~.................................................::~YY~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^~?5!.                ^JP5JJJJJJJJJJJJJJJJJJJJJYGY:       ^55JJJP?.            
^       :YY~^^^^^^^^^^^^^^^^^^^^^^^^^^^^~?P5YJYP7^^!YP5JJJJJJJJJJJJJJYPP~..................................................................::^^....................::::::::::::::::::.............~J!~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^~JG7.              :?55JJJJJJJJJJJJJJJJJJJJJJ5GY^        .?PJJYP!.            
^       :Y5!^^^^^^^^^^^^^^^^^^^^^^^^^^^^^~7YP5YPJ~^^~7YP5JJJJJJJJJJJJJ5P7......................................................................................::::::::::::::::::::::::::::::....^?7~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^~YP!.            :7Y5YJJJJJJJJJJJJJJJJJJJJJJ557:         .!PYJ5P~.            
^       .7P7^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^~7YY55!^^^^~75P5YJJJJJJJJJJJ5P7:.....:::::::::::::::::::.........................................................:::::::::::::::::::::::::::::::.....^?Y~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^!55^           ^7Y5YJJJJJJJJJJJJJJJJJJJJJJY5?:           .~P5JPJ:             
^        ^5J~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^!?J7~^^^^^~7Y55YJJJJJJJJJJ5P?:.::::::::::::::::::::::::::::::...............................................::::::::::::::::::::::::::::::::::..::757~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^~7P?.        :!?5YJJJJJJJJJJJJY55JJJJJJJJY5?^              ^Y5YY^              
^        :J5!^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^~~~^^^^^^^~!?5P5YYJJJJJJJY5Y~.::::::::::::::::::::::::::::::.............................................::::::::::::::::::::::::::::::::::::::!5J~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^~7P7.     .~J5PYJJJJJJJJJJJJYPPYJJJJJJJ5PY!.               ^Y55?.              
^        .~57^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^~Y5?J5P5JJJJJJJ55?^:::::::::::::::::::::::::::::::.........................................:::::::::::::::::::::::::::::::::::::::~YP7~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^~75~   .^?5P5YJJJJJJJJJJJJJYPPJJJJJJJ55J!.                .~55Y^               
^         :JJ~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^~?P?:.^!?Y55YYJJJJYY7^:::::::::::::::::::::::::::::..:............:!^......:......:~^......:::::::::::::::::::::::::::::::::::::::^?PJ~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^~?P!.^?555YJJJJJJJJJJJJJJJJPPJJJJJJ5PJ^.                  .7PP7.               
^         .!5!^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^!PP!:....:!JJ?JYYYYY5Y7^:::::::::::::::::::::::::::::::...........:7J?7!777777?7!?YY!:....::::::::::::::::::::::::::::::::::::::::!P5!~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^~?PYY55YJJJJJJJJJJJJJJJJJJPPYJJJJYPY~.                    ^YPJ:                
^          :YJ~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^~7P7:...::..::::^^~!!77!^::::::::::::::::::::::::::::::...............::::.....::::::.....::::::::::::::::::::::::::::::::::::::::^JP?~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^!5G5JJJJJJJJJJJJJJJJJJJJJYG5JJJJ5P?:                     .7GY^                 
^          .7P7~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^!YP!:..::::::::::::::::::::::::::::::::::::::::::::::::..................................:::::::::::::::::::::::::::::::::::::::::!PY~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^!5PJJJJJJJJJJJJJJJJJJJJJJPPJJJJ55!.                      ^5Y:                  
^           ^55!^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^!YP~....:::::::::::::::::::::::::::::::::::::::::::::::.................................:::::::::::::::::::::::::::::::::::::::::^J57~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^!55JJJJJJJJJJJJJJJJJJJJJYP5JJJ5J:                       :Y5^                   
^           .!P?~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^~JP!....::::::::::::::::::::::::::::::::::::::::::::::::.................................:::::::::::::::::::::::::::::::::::::::^7P?~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^!5YJJJJJJJJJJJJJJJJJJJJJYPYJJ5Y^                      .~YY^.                   
^            .75!^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^~7PJ:....::::::::::::::::::::::::::::::::::::::::::::::..................................:::::::::::::::::::::::::::::::::::::::~YY~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^~?PYJJJJJJJJJJJJJJJJJJJJJYPYJ5Y:  .!J~               :!5P7:                     
^             :YJ~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^~JP?:.:.::::::::::::::::::::::::::::::::::::::::::::::..................................:::::::::::::::::::::::::::::::::::::::75?~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^!Y5JJJJJJJJJJJJJJJJJJJJJJYPYYY^. .!55P?~^:::...::^7?Y5Y?^.                      
^             .!57^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^~?PJ:...:::::::::::::::::::::::::::::::::::::::::::::..................................::::::::::::::::::::::::::::::::::::::^??~~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^~?P5JJJJJJJJJJJJJJJJJJJJJJYP5Y~.  .7PYJY55YYYYJJYY5P5?!:                         
^              .7Y!^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^75Y^....::::::::::::::::::::::::::::::::::::::::::::.................................:::::::::::::::::::::::::::::::::::::^?Y!^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^~YPYJJJJJJJJJJJJJJJJJJJJJJJ5P?:    .!JYYJYYYYY55YJ?~:                            
^               :?Y!^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^~J5?^..::::::::::::::::::::::::::::::::::::::::::::................................::::::::::::::::::::::::::::::::::::::~J?~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^~7P5JJJJJJJJJJJJJJJJJJJJJJJJPP7.      .:^~~!!~^::.                                
^                :J?~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^~7557:....:::::::::::::::::::::::::::::::::::::::..................................::::::::::::::::::::::::::::::::::::^?Y!^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^~JPJJJJJJJJJJJJJJJJJJJJJJJJJ5P7.                                                  -->
