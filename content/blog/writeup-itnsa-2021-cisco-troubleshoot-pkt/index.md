+++
title = "Writeup ITNSA 2021 - Packet Tracer Troubleshoot"
date = "2022-09-09"
description = "Troubleshooting ITNSA 2021 Challenges."
tags = [
    "CISCO",
    "ITNSA",
    "Packet Tracer",
    "DHCP",
    "RADIUS",
    "NTP",
    "OSPF",
    "GRE OVER IPSEC",
    "IPSEC",
    "ASA FIREWALL"
]
+++

---

<a name="top"></a>

{{< toc >}}

--- 

## Introduction
ITNSA  <br>

Let's try it out. as usual, this question is answered with [Cisco Packet Tracer](https://www.netacad.com/courses/packet-tracer) <br>
Those who want to try it can get it from this [ITNSA](https://drive.google.com/file/d/1RjGfsr6ZeZ-fXWFsbkNDqpOqV4aBJqyH/view?usp=sharing)

{{< bundle-image name="ITNSA-TOPOLOGY.PNG" alt="TOPOLOGY" caption="Topology ITNSA Module B" >}}

---

## Troubleshooting 1 (Point: 25)
### Questions
All hosts on LAN BRANCH1 could not communicate to LAN BRANCH2 and vice versa. <br>
However, the Internet connection from each BRANCH to the Internet was successful.

### Answers

In BRANCH1 there is an typo in the ospf section in the network section 192.168.0.2/30.<br>
In BRANCH2 there is an missing in the opsf section in the network section 192.168.3.0/24.<br>

#### BRANCH1

Configuration :

```HTML
BRANCH1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
BRANCH1(config)#router ospf 1
BRANCH1(config-router)#network 192.168.0.2 0.0.0.3 area 0
BRANCH1(config-router)#no network 192.168.0.2 0.0.0.0 area 0
23:09:50: %OSPF-5-ADJCHG: Process 1, Nbr 203.0.113.18 on Tunnel0 from FULL to DOWN, Neighbor Down: Interface down or detached

23:09:50: %OSPF-5-ADJCHG: Process 1, Nbr 203.0.113.18 on Tunnel0 from LOADING to FULL, Loading Done
```

Before :

```HTML
BRANCH1#sh run | section ospf
router ospf 1
 log-adjacency-changes
 network 192.168.0.2 0.0.0.0 area 0
 network 192.168.2.0 0.0.0.255 area 0
```

After :

```HTML
BRANCH1(config-router)#do sh run | section ospf
router ospf 1
 log-adjacency-changes
 network 192.168.2.0 0.0.0.255 area 0
 network 192.168.0.0 0.0.0.3 area 0
```

#### BRANCH2


Configuration : 
```HTML
BRANCH2#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
BRANCH2(config)#router ospf 1
BRANCH2(config-router)#network 192.168.3.0 0.0.0.255 area 0
23:09:36: %OSPF-5-ADJCHG: Process 1, Nbr 203.0.113.18 on Tunnel0 from LOADING to FULL, Loading Done
```

Before : 

```HTML
BRANCH2#sh run | section ospf
router ospf 1
 log-adjacency-changes
 network 192.168.0.4 0.0.0.3 area 0
```

After : 

```HTML
router ospf 1
 log-adjacency-changes
 network 192.168.0.4 0.0.0.3 area 0
 network 192.168.3.0 0.0.0.255 area 0
```
---

Simple PDU

{{< bundle-image name="SIMPLEPDU.PNG" alt="Simple PDU from each other client and BRANCH">}}

PING

{{< bundle-image name="PING.PNG" alt="Ping from each other client">}}

OSPF

{{< bundle-image name="IP-ROUTE-BRANCH1.PNG" alt="Show IP Route from OSPF BRANCH 1">}}

{{< bundle-image name="IP-ROUTE-HQ.PNG" alt="Show IP Route from OSPF HQ">}}

{{< bundle-image name="IP-ROUTE-BRANCH2.PNG" alt="Show IP Route from OSPF BRANCH 2">}}

---

## Troubleshooting 2 (Point: 25)
### Questions
HQ, BRANCH1 and BRANCH2 routers failed to synchronize the time with the Network Time Protocol (NTP) server on the Internet. <br>

### Answers
So I checked the configuration of the ROOT DNS + NTP SERVER in the Services> NTP section that the password used is "cisco", let's adjust it with the others <br>

---
NTP Server Password

{{< bundle-image name="NTP-SERVER.PNG" alt="NTP Server password">}}

---

#### ROUTER HQ

Configuration : 

```HTML
HQ#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
HQ(config)#ntp authentication-key 1 md5 cisco
```

Before : 

```HTML
HQ#sh ntp associations

address         ref clock       st   when     poll    reach  delay  offset    disp
 ~203.0.113.1   .AUTH.          16   -        64      0      0.00   0.00      16000.00
 * sys.peer, # selected, + candidate, - outlyer, x falseticker, ~ configured
```

```HTML
HQ#sh run | section tp
ntp authentication-key 1 md5 08221D5D0A49 7
ntp trusted-key 1
ntp server 203.0.113.1 key 1
```

After :

```HTML
HQ(config)#do sh ntp associations

address         ref clock       st   when   poll    reach  delay  offset           disp
 ~203.0.113.1   127.127.1.1     1    14     16      1      17.00  899589635000.00  0.00
 * sys.peer, # selected, + candidate, - outlyer, x falseticker, ~ configured
```

```HTML
HQ(config)#do sh run | section ntp
ntp authentication-key 1 md5 0822455D0A16 7
ntp trusted-key 1
ntp server 203.0.113.1 key 1
```

#### BRANCH1

Configuration :

```HTML
BRANCH1#en
BRANCH1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
BRANCH1(config)#ntp authentication-key 1 md5 cisco 
```

Before :

```HTML
BRANCH1#sh ntp associations 

address         ref clock       st   when     poll    reach  delay   offset   disp
 ~203.0.113.1   .AUTH.          16   -        64      0      0.00    0.00     16000.00
 * sys.peer, # selected, + candidate, - outlyer, x falseticker, ~ configured
```

```HTML
BRANCH1#sh run | section ntp 
ntp authentication-key 1 md5 08221D5D0A49 7
ntp trusted-key 1
ntp server 203.0.113.1 key 1
```

After : 

```HTML
BRANCH1(config)#do sh ntp associations

address         ref clock       st   when  poll reach  delay  offset            disp
 ~203.0.113.1   127.127.1.1     1    5     16   1      19.00  899589634999.00   0.00
 * sys.peer, # selected, + candidate, - outlyer, x falseticker, ~ configured
```

```HTML
BRANCH1(config)#do sh run | section ntp
ntp authentication-key 1 md5 0822455D0A16 7
ntp trusted-key 1
ntp server 203.0.113.1 key 1
```

#### BRANCH2

Configuration :

```HTML
BRANCH2#en
BRANCH2#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
BRANCH2(config)#ntp authentication-key 1 md5 cisco 
```
Before :

```HTML
BRANCH2#sh ntp associations

address         ref clock       st   when     poll    reach  delay   offset   disp
 ~203.0.113.1   .AUTH.          16   -        64      0      0.00    0.00     16000.00
 * sys.peer, # selected, + candidate, - outlyer, x falseticker, ~ configured
```

```HTML
BRANCH2#sh run | section ntp
ntp authentication-key 1 md5 08221D5D0A49 7
ntp trusted-key 1
ntp server 203.0.113.1 key 1
```

After : 

```HTML
BRANCH2(config)#do sh run | section ntp
ntp authentication-key 1 md5 0822455D0A16 7
ntp trusted-key 1
ntp server 203.0.113.1 key 1
```

```HTML
BRANCH2(config)#do sh run | section ntp
ntp authentication-key 1 md5 0822455D0A16 7
ntp trusted-key 1
ntp server 203.0.113.1 key 1
```

---

## Troubleshooting 3 (Point: 25)
### Questions
The network administrator has set up a Radius-based authentication scheme on the HQ Server for Router HQ. <br>

However, Network administrators can only log in to Router HQ utilizing local user accounts both console and SSH access.

### Answers
Yup, there is a typo in HQ router side configuration and in ServerHQ section the service for RADIUS is not turned on. <br>

#### ROUTER HQ

Configuration : 

```HTML
/// First, we need to delete old configuration
HQ(config)#no radius-server host 192.168.1.253 auth-port 1645
HQ(config)#no radius-server key PaSSwOrd
HQ(config)#do sh run | section radius
aaa authentication login SERVER-AAA group radius local
/// Then, we add new configuration
HQ(config)#radius server 192.168.1.254
HQ(config-radius-server)#address ipv4 192.168.1.254 auth-port 1645
HQ(config-radius-server)#key Pa55w0rd
 WARNING: Command has been added to the configuration using a type 0 password. However, type 0 passwords will soon be deprecated. Migrate to a supported password type
*Sep 09 21:25:23.129: %AAAA-4-CLI_DEPRECATED: WARNING: Command has been added to the configuration using a type 0 password. However, type 0 passwords will soon be deprecated. Migrate to a supported password type
```

Before : 

```HTML
HQ#sh run | section radius
aaa authentication login SERVER-AAA group radius local 
radius-server host 192.168.1.253 auth-port 1645
radius-server key PaSSwOrd
radius server 192.168.1.253
 address ipv4 192.168.1.253 auth-port 1645
```


After :

```HTML
HQ(config)#do sh run | sectio radius
aaa authentication login SERVER-AAA group radius local 
radius server 192.168.1.254
 address ipv4 192.168.1.254 auth-port 1645
 key Pa55w0rd
```

---

#### SERVERHQ

Before :

{{< bundle-image name="RADIUS-SERVER-BEFORE.PNG" alt="Radius Server after turn on Service for AAA">}}

After : 

{{< bundle-image name="RADIUS-SERVER-AFTER.PNG" alt="Radius Server after turn on Service for AAA">}}

Login Success :

{{< bundle-image name="RADIUS-SERVER-SUCCESS-LOGIN.PNG" alt="Radius Login Success">}}

---

## Troubleshooting 4 (Point: 25)
### Questions
All hosts on LAN HQ, BRANCH1 and BRANCH2 of PT. AR as well as Internet Client PCs on the Internet subnet cannot access the ntbprov.go.id site but other sites are successfully accessed. 

### Answers

Assign port Ethernet0/1 to be a member of VLAN 3. <br>
Change interface in out in object-network server-dmz.

#### ASA-NTBPROV

Configuration :

```HTML
ciscoasa>en
ciscoasa#conf t
ciscoasa(config)#int eth0/1
ciscoasa(config-if)#switchport access vlan 3
ciscoasa(config-if)#exit
ciscoasa(config)#object network server-dmz
ciscoasa(config-network-object)#nat (dmz,outside) static 203.0.113.34
```

Before :

{{< bundle-image name="ASA-FIREWALL-BEFORE.PNG" alt="ASA Firewall before configuration">}}

{{< bundle-image name="ASA-FIREWALL-DMZ-BEFORE.PNG" alt="ASA Firewall DMZ before configuration">}}

After : 

{{< bundle-image name="ASA-FIREWALL-AFTER.PNG" alt="ASA Firewall after configuration">}}

{{< bundle-image name="ASA-FIREWALL-AFTER.PNG" alt="ASA Firewall DMZ after configuration">}}

Verification :

{{< bundle-image name="CLIENT-SUCCESS.PNG" alt="Client can access ntbprov.go.id">}}

---

## Summary

The questions aren't too difficult, but I had trouble with a section of question 1 that had a bug (?).

The error "ISAKMP (0:1): Cannot start Aggressive mode, trying Main mode" appears occasionally in BRANCH1.<br>
The error "%CRYPTO-4-RECVD PKT INV SPI: decaps: received IPSEC packet has invalid spi for" appears at HQ.

It happens when I reload a.PKT file, so I have to try reloading several times to avoid getting that bug.

If I find a solution to these two bugs, I may update this post. <br>
See you in the next post 