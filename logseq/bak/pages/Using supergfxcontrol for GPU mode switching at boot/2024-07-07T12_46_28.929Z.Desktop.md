## Prerequisites
	- Needs a Hybrid Laptop; With integrated graphics and a dedicated graphics.
	- This controls gpu mode switching, similar to [envycontrol](https://github.com/bayasdev/envycontrol) and [optimus manager.](https://github.com/Askannz/optimus-manager) This can run (and is only supported) on Wayland like envycontrol but gpu mode can be picked on boot using kernel parameters. This also does not need a full reboot when switching modes and may only need a logout at most.
	- For Optimus Laptops, the NVIDIA proprietary drivers are needed and no other GPU switcher tool installed. For more info refer to [ for Arch Linux systems](https://wiki.archlinux.org/title/Supergfxctl) or your own distro's documentations.
- ## Installation and Setting up on KDE
	- Install from the [AUR](https://aur.archlinux.org/packages/supergfxctl/).
	- Enable the SystemD service
		- ```
		  sudo systemctl enable --now supergfxd.service
		  ```
	- Install the Plasma 6 applet from the [AUR.](https://aur.archlinux.org/packages/plasma6-applets-supergfxctl/)
	- Reboot
- ## Adding GPU modes selection on Refind Bootloader
	- Refer to ((6633b1dd-15b1-44ec-ace9-41dae827b72e)).