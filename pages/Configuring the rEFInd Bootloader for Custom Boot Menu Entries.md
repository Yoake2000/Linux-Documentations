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
	- Add the following line on the rEFInd