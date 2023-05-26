---
layout: default
---
[Back](./)

## Fresh Server Config

**Install and setup**

1. Update Packages and Repositories
`sudo apt-get update && sudo apt-get upgrade -y`
2. Configure Unattended-Upgrade
`sudo apt install unattended-upgrades`
`sudo dpkg-reconfigure --priority=low unattended-upgrades`
3. Disable Automatic Reboots
`sudo nano /etc/apt/apt.conf.d/50unattended-upgrades`
4. Uncomment the line below 
`\\Unattended-Upgrade::Automatic-Reboot "false";`

**SSH with Ed25519 keys**

1. Packages Install
`sudo apt-get install openssh-server`
2. Check if sshd is running
`sudo systemctl status sshd`
or 
`sudo sshd status`

**Check what keys you have (If any)**

1. List all your keys
`for keyfile in ~/.ssh/id_*; do ssh-keygen -l -f "${keyfile}"; done | uniq`
2. Check below 

| **Type**      | **Security** |
|---------------|--------------|
| DSA           | Unsafe!      |
| RSA 1024 bits | Unsafe!      |
| RSA 2048      | Okay!        |
| RSA 3072/4096 | Great!       |
| ECDSA         | Good!        |
| Ed25519       | Amazing!     |

**Upgrade your SSH keys to Ed25519**

1. Start by creating a new user and authorizing SSH-based access for an SSH key pair.
```bash
sudo adduser <auser>
sudo usermod -aG sudo <asuser> 
mkdir ${HOME}/.ssh
chmod 700 ~/.ssh
```
2. Create Ed25519 key
```bash
cd ~/.ssh
ssh-keygen -o -a 100 -t ed25519 -f id_ed # Your public key has been saved in id_ed.pub
cat id_ed.pub
```
3. Copy the public key from the client and head back on the server 
`echo "your-public-key" >> ~/.ssh/authorized_keys`
4. Back on the client
`ssh -i ${HOME}/.ssh/id_ed <serveruser>@192.168.50.45`
5. Disable root login and password authentication 
`sudo nano /etc/ssh/sshd_config`
- Change the two lines below to match
```yml
# Authentication:
PermitRootLogin no
....
# To disable tunneled clear text passwords, change to no here!
PasswordAuthentication no
```
6. Restart ssh
`sudo systemctl restart ssh`

* * *

## Automate with BASH Scripting

**Creating a SSH keypai**
You can use this script to automate the key creation part on a newly imaged linux machine. 
```sh
#!/bin/bash

# Check if SSH key pair exists
if [[ -f ~/.ssh/id_rsa && -f ~/.ssh/id_rsa.pub ]]; then
    echo "SSH key pair already exists."
else
    echo "Generating SSH key pair..."
    ssh-keygen -t rsa -b 4096 -f ~/.ssh/id_rsa -N ""
    echo "SSH key pair generated."
fi
```

However this does not work if the machine does not already have OpenSSH installed for the command `ssh-keygen` to work.
So I needed to adjust the script to check for OpenSSH installition.

```sh
#!/bin/bash

# Check if OpenSSH is installed 
if ! command -v ssh >/dev/null 2>&1; then 
	echo "OpenSSH is not installed. Installing..." 
	apt-get update 
	apt-get install openssh-client -y 
	echo "OpenSSH installed." 
else 
	echo "OpenSSH is already installed." 
fi
```

**Copy keys to another server.**
`ssh-copy-id -i ~/.ssh/id_rsa.pub root@192.168.1.177`

**For Esxi I needed to do it this way**
The location of the keys are in a different location then usual
```sh
cat ~/.ssh/id_rsa.pub | ssh root@10.1.1.11 'cat >> /etc/ssh/keys-root/authorized_keys' 
```

[Back](./)
