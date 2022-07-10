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
	```sh
	# Authentication:
	PermitRootLogin no
	....
	# To disable tunneled clear text passwords, change to no here!
	PasswordAuthentication no
	```
5) Restart ssh
	`sudo systemctl restart ssh`
