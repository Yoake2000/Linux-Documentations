- This guide is based on this [lemmy.ml post](https://lemmy.ml/post/61254) which is also heavily based on this [blog post.](https://nerdstuff.org/posts/2020/2020-004_arch_linux_luks_btrfs_systemd-boot/) I have outlined here my steps in reinstalling my arch linux system after it had a hiccup which is time consuming to recover from and decided to nuke my system and reinstall it anew and might as well try to use BTRFS and TPM2.0 unlocking for my encrypted volumes.
- There are some problems I've encountered which deviate the steps a little from the previously said guides which maybe due to my setup.
- I've used the latest Arch Linux iso as of the writing of this guide [[Sep 22nd, 2024]] (2024.09.01 Release). I've also used Ventoy to load the iso from a usb. It has a little bit of quirk that the live environment wont boot if using normal mode and would instead need to be booted in grub2 mode. This maybe due to a bug in Ventoy.
	- I was planning to migrate from ventoy sometime in the future since it's been giving me problems in booting to live environments lately and specially with the [issue](https://lemmy.ml/post/20404494) the other day.
	- Considering to use a fork or `glim`
- # Base Install
- An internet connection is needed for the whole process. I used an Ethernet cable but a WiFi connection can also be used but it needs to be configured first as outlined in the [Arch Wiki.](https://wiki.archlinux.org/title/Iwd#iwctl) For other connection types, refer to the [Arch Wiki Installation page.](https://wiki.archlinux.org/title/Installation_guide#Connect_to_the_internet)
- The system clock is to be synced first
	- ```
	  # timedatectl set-ntp true
	  ```
	- My first problem occurred here. Maybe due to the ISP servers or some unknown reason, my system clock can't sync with the