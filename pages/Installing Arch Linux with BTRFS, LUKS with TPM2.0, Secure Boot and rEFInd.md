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
	- I've also created a swap partition right before the root partition. From the guides, they're using a swap file inside the encrypted partition. I may use that in the future and will be editing this guide. As for the hex code for the swap partition, it is `8200`.
- Encrypting the Disks
	- First is to create an encrypted container for the root file system. A passphrase is needed to be defined.
		- ```
		  # cryptsetup luksFormat /dev/sda2
		  ```
	- The encrypted container is needed to be unlocked for it to be used. Same with the guides, I used `luks` as the placeholder here.
		- ```
		  # cryptsetup open /dev/sda2 luks
		  ```
- File System Creation
	- Format the EFI partition with FAT32 and give it the label `EFI` - you can choose any other label name:
	  collapsed:: true
		- ```
		  # mkfs.vfat -F32 -n EFI /dev/sda1
		  ```
	- Format the root partition with Btrfs and give it the label `ROOT` - you can choose any other label name. If you didn’t open the LUKS container under the name “`luks`” you must adjust the command accordingly:
	  collapsed:: true
		- ```
		  # mkfs.btrfs -L ROOT /dev/mapper/luks
		  ```
	- Create and Mount Sub Volumes
	  collapsed:: true
		- Create [sub volumes](https://wiki.archlinux.org/index.php/Btrfs#Subvolumes) for root, home, the package cache, and the entire Btrfs file system:
		- ```
		  # mount /dev/mapper/luks /mnt
		  # btrfs sub create /mnt/@
		  # btrfs sub create /mnt/@home
		  # umount /mnt
		  ```
		- The guide also made a sub volume for the snapshots. I will be using `timeshift` to create snapshots so it was not needed. Maybe in the future I will use it and configure my system accordingly.
		- Mount the sub volumes:
		- ```
		  # mount -o noatime,nodiratime,compress=zstd,space_cache=v2,ssd,subvol=@ /dev/mapper/luks /mnt
		  # mkdir -p /mnt/{boot,home}
		  # mount -o noatime,nodiratime,compress=zstd,space_cache=v2,ssd,subvol=@home /dev/mapper/luks /mnt/home
		  ```
		- Mount the EFI partition
		- ```
		  # mount /dev/nvme0n1p1 /mnt/boot
		  ```
	- Install Arch Linux (adjust this list to your needs):
	- collapsed:: true
	  ```
	  # pacstrap /mnt linux-zen linux-firmware base btrfs-progs intel-ucode vim
	  ```
		- I used `linux-zen` and `vim` here. The kernel lets me use `waydroid` without any new kernel modules but I must use the `dkms` version of nvidia drivers for the dgpu to work.
	- Generate `/etc/fstab`:
	- ```
	  # genfstab -U /mnt >> /mnt/etc/fstab
	  ```
	- System Configuration
		- `chroot` into the new system:
		- ```
		  # arch-chroot /mnt/
		  ```
		- Set host name:
		- ```
		  # echo <YOUR-HOSTNAME> > /etc/hostname
		  ```
		- create a user and a password, first the root password:
		- ```
		  # passwd
		  ```
		- Create a user
		- ```
		  # useradd -mG wheel <YOUR-USERNAME>
		  ```
		- Now edit the sudoers file to give your user sudo permissions. I used `vim` as my text editor:
			- ```
			  # EDITOR=vim visudo
			  ```
		- Uncomment the following
		- ```
		  %wheel ALL=(ALL) ALL
		  ```
		- Add a password for the user
		- ```
		  passwd <YOUR-USERNAME>
		  ```
		- Set locale:
		  collapsed:: true
			- ```
			  # echo LANG=en_US.UTF-8 > /etc/locale.conf
			  ```
		- Uncomment the following rows of `/etc/locale.gen`:
		- ```
		  #en_US.UTF-8 UTF-8
		  ```
			- I've uncommented out more lines so that I can use some more language and locales.
		- Generate locale:
		- ```
		  # locale-gen
		  ```
		- Set time zone:
		- ```
		  # ln -sf /usr/share/zoneinfo/Europe/Berlin /etc/localtime
		  ```
		- Define hosts in `/etc/hosts`:
		- ```
		  #<ip-address>	<hostname.domain.org>	<hostname>
		  127.0.0.1	<YOUR-HOSTNAME>.localdomain	<YOUR-HOSTNAME>
		  ::1		localhost.localdomain	localhost
		  ```
	- Configuring the initramfs
		- Configure the creation of [initramfs](https://wiki.archlinux.org/index.php/Arch_boot_process#initramfs) by editing `/etc/mkinitcpio.conf`. Change the line `HOOKS=...` to:
		- ```
		  HOOKS=(base udev systemd autodetect keyboard modconf block sd-encrypt filesystems resume)
		  ```
		- Recreate initramfs:
			- ```
			  # mkinitcpio -P
			  ```
	- Install some more useful packages. (Apparmor and Refind package included here)
	- ```
	  pacman -S linux-zen-headers networkmanager dialog wpa_supplicant mtools dosfstools git xdg-utils xdg-user-dirs alsa-utils pipewire pipewire-alsa pipewire-pulse apparmor refind
	  ```
	- Install `REFind` as the boot loader
	- ```
	  # refind-install
	  ```
	- Edit `/boot/EFI/refind/refind.conf` and  add the following entry at the bottom. More info on configuring the `REFind` bootloader on [[Configuring the rEFInd Bootloader for Custom Boot Menu Stanzas]] Necessary parts here are the `rd.luks` and `root` parameters for the encrypted drives.
	- ```
	  menuentry "Arch Linux" {
	      icon     /EFI/refind/themes/darkmini/icons/os_arch.png
	      volume   "Arch Linux"
	      loader   /vmlinuz-linux
	      initrd   /initramfs-linux.img
	      options  " rd.luks.name=<UUID OF ROOT PARTITION>=luks root=/dev/mapper/luks rootflags=subvol=@ rd.luks.options=<UUID AGAIN>=discard rw quiet lsm=lockdown,yama,apparmor,bpf quiet splash loglevel=3 systemd.show_status=auto rd.udev.log_level=3 nvidia_drm.modeset=1 amd_pstate=active acpi_enforce_resources=lax iommu=soft amd_iommu=fullflush"
	      }
	  }	
	  ```
	- Exit the `chroot` and reboot the system.
- # Installing KDE,Yay, Timeshift and the Nvidia Drivers.
- After logging on the system, A desktop environment can then be installed along with the other drivers such as the Nvidia GPU drivers.
	- For my system I installed the KDE Plasma DE
		- ```
		  $ sudo pacman -Syu plasma-meta
		  ```
		- More info on the [Arch wiki.](https://wiki.archlinux.org/title/KDE)
		- Needs to install more applications such as `dolphin` for file manager
		- Enable the Display Manager and Network Manager services.
		- ```
		  systemctl enable sddm.service
		  systemctl enable NetworkManager.service
		  ```
	- Nvidia Drivers
		- Since I'm using the `linux-zen` kernel and want to use the beta drivers I first need to install `yay`, an AUR helper.
		- ```
		  $ sudo pacman -S --needed git base-devel && git clone https://aur.archlinux.org/yay.git && cd yay && makepkg -si
		  ```
		- Then install the Nvidia drivers
		- ```
		  $ yay -S nvidia-open-beta-dkms nvidia-utils-beta
		  ```
		- Install `timeshift`
		- ```
		  $ sudo pacman -Syu timeshift
		  ```
- # Enabling Secure Boot and TPM 2.0
	- Enabling Secure Boot in `rEFind` using Pre Signed Bootloader.
		- Install `preloader-signed` from the `AUR`
		- ```
		  $ yay -Syu preloader-signed
		  ```
		- Run `refind-install` with some options
		- ```
		  # refind-install --preloader /usr/share/preloader-signed/PreLoader.efi
		  ```
		- After a reboot with Secure Boot enabled from the `UEFI` firmware menu, HashTool will launch and it is needed to enroll the hash of rEFInd `loader.efi` and the kernel `vmlinuz-linux-zen`
	- TPM 2.0
		- Check if the system supports TPM 2.0
		- ```
		  cat /sys/class/tpm/tpm0/device/description
		  ```
		- ```
		  /sys/class/tpm/tpm0/tpm_version_major
		  ```
		- If the system supports it, run the following command to check if the system is configured properly (from the initramfs hooks earlier)
		- ```
		  systemd-cryptenroll --tpm2-device=list
		  ```
		- Run the following to enroll the TPM 2.0 key to our encrypted drive `/dev/nvme0n1p3`
		- ```
		  $ sudo systemd-cryptenroll --tpm2-device=auto --tpm2-pcrs=0,7 /dev/nvme0n1p3
		  ```
		- Add the following to the bootloader entry
		- ```
		  rd.luks.options=tpm2-device=auto
		  ```
	-