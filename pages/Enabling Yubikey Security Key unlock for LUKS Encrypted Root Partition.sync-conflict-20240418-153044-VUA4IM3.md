- [[#red]]==**Note: Copied From an .md file written on November 8,2023**==
- Note: The LUKS Encrypted Partitions used in my workstation is created by the Arch Linux Script Installer and steps outlined here may differ for case to case basis. The home partition was also encrypted by the script and its decryption follows after the decryption of the root partition. Configurations pertaining to LUKS encryption except for the ones changed in the following steps are unchanged from stock (script installer).
- Add "sd-encrypt" hook to /etc/mkinitcpio.conf (To test replacing encrypt with sd-encrypt). Currently running both hooks.
	- Relevant discussion: https://www.reddit.com/r/archlinux/comments/1307cke/encrypt_vs_sdencrypt_mkinitcpio_hooks/)
- Create file /etc/crypttab.initramfs
  
    ```
    root    UUID=XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX  none  tpm2-device=auto
  ```
- Regenerate initramfs. 
  
    ```
    # sudo mkinitcpi -P
  ```
- Insert Yubikey. Confirm if key is detected 
  
    ```
    $ systemd-cryptenroll --fido2-device=list
  ```
- Register key in LUKS slot. 
  
    ```
    $ systemd-cryptenroll --fido2-device=auto /dev/sdX
  ```
  
    /dev/sdX stands for the encrypted partition.
- ## Relevant Links:
  
  https://askubuntu.com/questions/848225/how-to-find-uuid-for-setting-up-luks-with-keyfile-protection-for-external-drive
  
  https://wiki.archlinux.org/title/Universal_2nd_Factor#Non-root_partitions
  
  https://wiki.archlinux.org/title/Dm-crypt/System_configuration#Trusted_Platform_Module_and_FIDO2_keys
  
  https://0pointer.net/blog/unlocking-luks2-volumes-with-tpm2-fido2-pkcs11-security-hardware-on-systemd-248.html
  
  https://bbs.archlinux.org/viewtopic.php?id=276892 (Discusses an old issue that may have been fixed as of writing)