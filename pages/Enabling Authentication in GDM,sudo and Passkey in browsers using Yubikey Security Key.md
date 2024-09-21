- {{renderer :tocgen2}}
- Note: Copied From an .md file written on November 8,2023
  background-color:: red
- Note: The machine that is used here is running archlinux and is using yay as a third party package manager to be able to download AUR packages conviniently.
- ```
  $ yay -S libfido2 pam-u2f yubico-authenticator-bin
  ```
- Add the key 
  id:: 661dd702-b5fa-4e99-8259-534692b87edf
  
    ```
    $ mkdir ~/.config/Yubico 
    $ pamu2fcfg -o pam://hostname -i pam://hostname > ~/.config/Yubico/u2f_keys
  ```
  
  `hostname` should be changed.
- ## For sudo
  id:: 661dd702-e1c2-4803-9b49-7ff141bd8e72
	- Add the following line to `/etc/pam.d/sudo` as the first line.
	  id:: 661dd702-6034-4cc7-bea7-265c1ab9385e
	  
	    ```
	    auth    sufficient  pam_u2f.so cue origin=pam://hostname appid=pam://hostname
	  ```
	  
	    `hostname` should be changed. Also `sufficient` there indicates that the following authentication is sufficient to be used. If 2FA is desired, where the key and a password is required, changing it to `required` is necessary. 
	  
	    This would make the security key as one of the ways to run sudo. There is still the password fallback. To remove the fallback, comment out the following line from the file
	  
	    ```
	    auth    include system-auth`
	  ```
	  
	    into
	  
	    ```
	    #auth   include     system-auth
	  ```
- ## For GDM
	- Add the following line to `/etc/pam.d/gdm-password` after the existing `auth` lines
	  
	    ```
	    auth    required    pam_u2f.so nouserok origin=pam://hostname appid=pam://hostname cue
	  ```
	  
	    `hostname` should be changed accordingly. The cue at the end of the line gives a prompt to touch the device to unlock.
	  
	    This would still require the password. Changing `required` into `sufficient` from the line and moving it as the first line in the `auth` parts would make the hardware key as sufficient unlocking tool with the password as fallback. The problem with this is that upon loggin in, Gnome Keyring would then ask for the password phrase. To remove it, change the Gnome Keyring password into blank. It can be done using `seahorse` (installable using the package manager). This theoretically makes your password and data less secure. My root and home partitions are encrypted though and for my use case I prefer a little bit of convenience over security. ~~In the future, I will be dabbling in using the key to also unlock Gnome Keyring.~~ This is difficult and there have been discussions to implement this within Gnome Keyring itself. There's also an [old implementation](https://github.com/recolic/gnome-keyring-yubikey-unlock) but has since been abandoned and nonfunctional. I've since then moved to KDE Plasma 6 and steps to implement a Yubikey authentication for it is outlined in [[YubiKey Security Key For KDE Plasma 6 SDDM and lockscreen login in Linux]]. 
	  
	    
	  
	    Relevant Links: https://221b.uk/gnome-login-using-u2f-security-tokens
	  
	                    https://askubuntu.com/questions/1167691/passwordless-login-with-yubikey-5-nfc
- ## For Browsers
	- Open the Yubico Authenticator Program with the key inserted on the device. Add a PIN to be assigned to the key. The key is now usable for websites that supports it.
- # Notes
  id:: 661df029-bbd1-4f3e-bf00-005aa8c2dc69
	- `nouserok` flag of `pam_u2f` : Set to enable authentication attempts to succeed even if the user trying to authenticate is not found inside authfile or if authfile is
	  missing/malformed. From: https://developers.yubico.com/pam-u2f/Manuals/pam_u2f.8.html
- ## Relevant Links:
  
  https://wiki.archlinux.org/title/Universal_2nd_Factor
  
  https://www.reddit.com/r/Fedora/comments/akck9m/authenticating_with_gdm_and_sudo_with_a_u2f/