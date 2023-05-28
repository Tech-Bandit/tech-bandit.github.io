---
layout: default
---
[Back](./)

## Current DC Lab Infrastructure with PFsense
![Lab](./assets/VMInfra.jpg)

## VMWare EXXi Requirements 
First we need two virtual switches for **Internal** and **External** communications. 
> Networking -> Virtual Switch -> Add standard virtual switch

![VSwitch1](./assets/VMwareSwitch.png)

Create the proper Port Groups for each of the VLAN network like below. Later they will be used to connect the VMs.

![VSwitch2](./assets/VSwitch0.png)
![VSwitch3](./assets/VMwareSwitch1.png)

|Port Groups | VLAN ID |
|:----|:----|
| LAN (Internal) | 0 |
| Server (Internal) | 11 |
| Client (Internal) | 10 |
| ALL_Trunk (Internal) | 4096 |
| ISP (External) | 0 |

![PortGroup](./assets/PortGroup.png)

## PfSense Requirements
Create a Pfsense VM and add these networks 
![[Pfsense.png]]
Create Interface Assignments and enable DHCP on all of the Interfaces. Later we will make it also assign the DC's as the DNS resolver.

| Network | Address | DNS |
|:----|:----|:----|
| WAN | 192.168.1.200/24 | DHCP (Default) | 
| LAN | 172.16.1.100/24 |  | Router (Default) |
| CLIENT | 172.16.10.100/24 | 172.16.1.10 (AD-DC) |
| SERVER | 72.16. 11.100/24 | Router (Default) |





