Blue Team Defense Notes

# Security

## Fresh Server Config

### Secure setup 

##### Install and setup

1) Packages and Repositories

	`sudo apt-get update && sudo apt-get upgrade -y`

2) Configure Unattended-Upgrade

	`sudo apt install unattended-upgrades`
	`sudo dpkg-reconfigure --priority=low unattended-upgrades`

3) Disable Automatic Reboots

	`sudo nano /etc/apt/apt.conf.d/50unattended-upgrades`
	
4) Uncomment the line below 

	`\\Unattended-Upgrade::Automatic-Reboot "false";`

##### SSH with Ed25519 keys

1) Packages Install

	`sudo apt-get install openssh-server`

2) Check if sshd is running

	`sudo systemctl status sshd`

	or 

	`sudo sshd status`

##### Check what keys you have (If any)

1) List all your keys

	`for keyfile in ~/.ssh/id_*; do ssh-keygen -l -f "${keyfile}"; done | uniq`

2) Check below 

| **Type**      | **Security** |
|---------------|--------------|
| DSA           | Unsafe!      |
| RSA 1024 bits | Unsafe!      |
| RSA 2048      | Okay!        |
| RSA 3072/4096 | Great!       |
| ECDSA         | Good!        |
| Ed25519       | Amazing!     |

##### Upgrade your SSH keys to Ed25519 

1) Start by creating a new user and authorizing SSH-based access for an SSH key pair.

	```sh
	sudo adduser <auser>
	sudo usermod -aG sudo <asuser> 
	mkdir ${HOME}/.ssh
	chmod 700 ~/.ssh
	```

2) Create Ed25519 key

	```sh
	cd ~/.ssh
	ssh-keygen -o -a 100 -t ed25519 -f id_ed # Your public key has been saved in id_ed.pub
	cat id_ed.pub
	```
3) Copy the public key from the client and head back on the server 

	`echo "your-public-key" >> ~/.ssh/authorized_keys`

4) Back on the client

	`ssh -i ${HOME}/.ssh/id_ed <serveruser>@192.168.50.45` 

5) Disable root login and password authentication 

	`sudo nano /etc/ssh/sshd_config`

	- Change the two lines below to match

	```
	# Authentication:
	PermitRootLogin no
	....
	# To disable tunneled clear text passwords, change to no here!
	PasswordAuthentication no
	```
5) Restart ssh

	`sudo systemctl restart ssh`





# Create and Manage Cloud Resources

- Create an instance

> - Name the instance .
> - Use an f1-micro machine type.
> - Use the default image type (Debian Linux).

- Create a 3-node Kubernetes cluster 

> Create a cluster (in the us-east1-b zone) to host the service.
> Use the Docker container hello-app (gcr.io/google-samples/hello-app:2.0) as a placeholder.
> Expose the app on port <APP PORT> 

- Create an HTTP(s) load balancer in front of two web servers

> ```
> cat << EOF > startup.sh
> #! /bin/bash
> apt-get update
> apt-get install -y nginx
> service nginx start
> sed -i -- 's/nginx/Google Cloud Platform - '"\$HOSTNAME"'/' /var/www/html/ index.nginx-debian.html
> EOF
> ```

> Create an instance template.
> Create a target pool.
> Create a managed instance group.
> Create a firewall rule named as to allow traffic (80/tcp).
> Create a health check.
> Create a backend service, and attach the managed instance group with named port (http:80).
> Create a URL map, and target the HTTP proxy to route requests to your URL map.
> Create a forwarding rule.
