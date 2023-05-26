---
layout: default
---
[Back](./)

## Disrupt IP Camera
In this test I wanted to see if Blink cameras are vulnerable to jamming and cause them to not record. The diagram of how the Deauth attack will take place is below. Once we find out the Mac address of the Sync Module we can send deauth packets at it using Airmon-ng and for the duration of the attack non of the cameras will be able to record or save its recordings.
![deauth](./assets/BlinkInfra.png)

### Requirments 
- Kali (Running in Virtualbox)
- Network adapter that can run on monitor mode. (Using Alpha AC1200)
> Followed these steps to install drivers
> 1. Update Repository
> `sudo apt upgrade -y`
> `sudo apt dist-upgrade -y`
> 2. Reboot the VM
> `sudo apt install realtek-rtl88xxau-dkms`
> `git clone [https://github.com/aircrack-ng/rtl8812au`
> `cd rtl8812au/ `
> `make`
> `sudo make install`

### Setup 
You'll need the BSSID of the Blink Sync Module, I am on the same network and Identiffied the IP and MAC of it. Blink is a Amazon product so looking up this mac address brings back this info,
[Mac Address Lookup Site](maclookup.app)
**Amazon Technologies Inc.**
You can also use AngryIPScanner
![IP](./assets/Scanned_IP.png)
