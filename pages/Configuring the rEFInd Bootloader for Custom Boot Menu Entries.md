# Prerequisites
	- The rEFInd Bootloader must be installed. Instructions on how to set it up along with using it with secure boot are found on the [Arch Wiki](https://wiki.archlinux.org/title/REFInd)
	- A very comprehensive documentation on how to Configure rEFInd is found [here.](https://www.rodsbooks.com/refind/configfile.html) This page would only discuss on adding Boot menu entries with custom kernel parameters for my Arch Linux System and adding a theme.
- # Adding and Applying a theme
	- I used the darkmini theme from this [github repo.](https://github.com/LightAir/darkmini) Other themes may be used but their instructions may differ.
	- Locate the rEFInd EFI directory. Commonly it is on `/boot/EFI/refind` like in my system. To determine the directory, one may consult the output of
		- ```
		  mount
		  ```
	- Create a `theme` folder on the previously mentioned directory
	-