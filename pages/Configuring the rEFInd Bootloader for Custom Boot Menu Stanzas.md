# Prerequisites
	- The rEFInd Bootloader must be installed. Instructions on how to set it up along with using it with secure boot are found on the [Arch Wiki](https://wiki.archlinux.org/title/REFInd)
	- A very comprehensive documentation on how to Configure rEFInd is found [here.](https://www.rodsbooks.com/refind/configfile.html) This page would only discuss on adding Boot menu entries with custom kernel parameters for my Arch Linux System and adding a theme.
- # Adding and Applying a theme
	- I used the darkmini theme from this [github repo.](https://github.com/LightAir/darkmini) Other themes may be used but their instructions may differ. There are also available themes from the AUR.
	- Locate the rEFInd EFI directory. Commonly it is on `/boot/EFI/refind` like in my system. To determine the directory, one may consult the output of
		- ```
		  mount
		  ```
	- Create a `themes` folder on the previously mentioned directory
	- Clone the theme inside the `themes` folder. This may need `root` privileges
		- ```
		  git clone https://github.com/LightAir/darkmini.git
		  ```
	- Add one of the following lines on the rEFInd config file `/boot/EFI/refind/refind.conf`
		- ```
		  include themes/darkmini/theme-mini.conf
		  ```
		- ```
		  include themes/darkmini/theme.conf
		  ```
	- The background image can be selected by setting the `banner` option in the theme file (From one of the previous lines).
		- ```
		  banner themes/darkmini/bg/ubuntu_style.png  
		  ```
- # Adding Custom Boot Menu Stanzas
	- By default, rEFInd auto detects installations and automatically add manual boot stanzas for them. For Linux systems, passing kernel options/parameters can be done on `/boot/refind_linux.conf`