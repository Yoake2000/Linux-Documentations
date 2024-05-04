# Prerequisites
	- A samba server must already be configured and is sharing a drive across the network.
		- I already had two drives shared through samba on my Raspberry Pi 4. The configuration was done through CasaOS. To set-up a samba server manually refer to the [Arch wiki.](https://wiki.archlinux.org/title/samba#Server)
	- samba must already be configured for client use on the computer. The [Arch wiki](https://wiki.archlinux.org/title/samba#Client) is a good place to refer for this for almost all Linux distros.
	-
- # Automount samba drive as mount entry
	- Edit the `/etc/fstab` file with the following entry
		- ```
		  //SERVER/sharename /mnt/mountpoint cifs _netdev,nofail,username=myuser,password=mypass 0 0
		  ```
	- From the Arch wiki. For my personal machine I used the following line:
		- ```
		  ```
-