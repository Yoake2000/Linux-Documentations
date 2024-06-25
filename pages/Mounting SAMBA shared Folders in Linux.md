## Prerequisites
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
		  //192.168.201.3/NetworkDrive /home/$USER/NetworkDrive cifs _netdev,credentials=<<CREDENTIAL LOCATION HERE>>,noauto,nofail,x-systemd.automount,cache=none,users,x-systemd.device-timeout=10ms 0 0 nodfs
		  ```
			- My credentials are stored in a file located somewhere in my root directory.
				- It has the following format
					- ```
					  username=value
					  password=value
					  ```
				- It is write-protected. I tried to only give my user account the permission to read the file but it breaks the mounting function in KDE Dolphin File Manager. As of now the permissions of the credential file is given by the following:
					- ```
					  #	chmod 744 <<CREDENTIAL FILE LOCATION>>
					  ```
			- The ``x-systemd`` options are for automounting only when accessed and for giving a time after booting before mounting.