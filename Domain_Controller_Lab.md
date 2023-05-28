---
layout: default
---
[Back](./)

## Current DC Lab Infrastructure with PFsense
![Lab](./assets/VMInfra.jpg)

## VMWare EXXi Requirements 
First we need two virtual switches for **Internal** and **External** communications. 
> Networking -> Virtual Switch -> Add standard virtual switch
![[VMwareSwitch.png]]
Create the proper Port Groups for each of the VLAN network like below. Later they will be used to connect the VMs.
![[VSwitch0.png]]
![[VMwareSwitch1.png]]

|Port Groups | VLAN ID |
|:----|:----|
| LAN (Internal) | 0 |
| Server (Internal) | 11 |
| Client (Internal) | 10 |
| ALL_Trunk (Internal) | 4096 |
| ISP (External) | 0 |

![[PortGroup.png]]
