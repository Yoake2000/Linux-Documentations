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
	- My first problem occurred here. Maybe due to the ISP servers or some unknown reason, my system clock can't sync.
	- Due to the clock not synced up with the servers, the installation environment can't automatically run some commands in order to sync up with their servers. This would cause some problems in the installation of packages. To resolve, the following should be run
		- ```
		  # pacman-key --init
		  # pacman-key --populate archlinux
		  # pacman -Sy archlinux-keyring
		  ```
		- The system installed after would also need its time to be synced up. I followed up the steps from the top comment of [this post.](https://www.reddit.com/r/archlinux/comments/15zgd4y/time_sync_wont_sync_help/)
		- Relevant Links
			- https://bbs.archlinux.org/viewtopic.php?id=292688
- Partitioning the Disks
	- For my setup I'm installing Arch on `/dev/nvme0`.
	- To partition the disk, we need to work on it using `gdisk` or `fdisk`.
		- ```
		  # gdisk /dev/nvme0
		  ```
	- Create a new partition table by selecting `o`
		- ```
		  Command (? for help): o
		  ```
	- Create an EFI partition (I chose a size of 1G to fit more kernels sometime in the future. The hex code is `EF00` for the boot partition):
		- ```
		  Command (? for help): n
		  ```
	- Create a root partition by adopting the default values.
		- ```
		  Command (? for help): n
		  ```
	- Now write the new partitions to the disk.
		- ```
		  Command (? for help): w
		  ```
	- I've also created a swap partition right before the root partition. From the guides, they're using a swap file inside the encrypted partition. I may use that in the future and will be editing this guide
- Encrypting the Disks
	-