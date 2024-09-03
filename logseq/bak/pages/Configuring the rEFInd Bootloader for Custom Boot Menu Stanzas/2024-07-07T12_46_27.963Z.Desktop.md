## Prerequisites
	- The rEFInd Bootloader must be installed. Instructions on how to set it up along with using it with secure boot are found on the [Arch Wiki](https://wiki.archlinux.org/title/REFInd)
	- A very comprehensive documentation on how to Configure rEFInd is found [here.](https://www.rodsbooks.com/refind/configfile.html) This page would only discuss on adding Boot menu entries with custom kernel parameters for my Arch Linux System and adding a theme.
	- The sample configuration file for rEFInd must first be copied by using
		- ```
		  #	cp /usr/share/refind/refind.conf-sample esp/EFI/refind/refind.conf
		  ```
- ## Adding and Applying a theme
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
  id:: 6633b1dd-15b1-44ec-ace9-41dae827b72e
	- By default, rEFInd auto detects installations and automatically add manual boot stanzas for them. For Linux systems, passing kernel options/parameters can be done on `/boot/refind_linux.conf`
	- Adding Custom Boot Menu Stanzas is done on `/boot/EFI/refind/refind.conf`. The configuration file is already extensively documented within its own file.
	- I use a custom boot menu stanza for my Arch Linux system. I first disabled the automatic scan by rEFInd by removing the `internal` option in the `scanfor` line. Be careful when doing this since if the custom boot menu stanza doesn't work (wrong options in the config file, etc) you wouldn't be able to boot easily into the system in rEFInd. You would need some other way to boot into the system or to edit the `refind.conf` file.
	- My current custom boot menu stanza is:
		- ```
		  menuentry "Arch Linux" {
		      icon     /EFI/refind/themes/darkmini/icons/os_arch.png
		      volume   "Arch Linux"
		      loader   /vmlinuz-linux
		      initrd   /initramfs-linux.img
		      options  "cryptdevice=PARTUUID=367c4973-3dd4-ed4c-ad44-d9990213dfc9:luksdev resume=UUID=da08223c-a742-4a82-9275-f02ec2703bba root=/dev/mapper/luksdev zswap.enabled=0 rw rootfstype=ext4 quiet splash loglevel=3 systemd.show_status=auto rd.udev.log_level=3 nvidia_drm.modeset=1 amd_pstate=active acpi_enforce_resources=lax iommu=soft amd_iommu=fullflush"
		      submenuentry "Boot to single-user mode" {
		          options	"cryptdevice=PARTUUID=367c4973-3dd4-ed4c-ad44-d9990213dfc9:luksdev root=/dev/mapper/luksdev zswap.enabled=0 rw rootfstype=ext4 single"
		      }
		      submenuentry "Boot with minimal options" {
		          options	"ro root=/dev/mapper/luksdev"
		      }
		      submenuentry "Integrated GPU mode"	{
		     	add_options "supergfxd.mode=Integrated"
		      }
		      submenuentry "Hybrid GPU mode"	{
		     	add_options "supergfxd.mode=Hybrid"
		      }
		  }
		  ```
		- The two last submenu's are for GPU switching modes by `supergfxcontrol`. Refer to [[Using supergfxcontrol for GPU mode switching at boot]] on how to set up `supergfxcontrol` in KDE Arch Linux.
- # Notes
	- Depending on how the bootloader partition/s are in the system, directories for this guide may vary. Refer to the [Arch Wiki](https://wiki.archlinux.org/title/REFInd) for more info
- # Relevant Sites
	- https://bbs.archlinux.org/viewtopic.php?id=258468
	- https://github.com/evanpurkhiser/rEFInd-minimal
	-
	-