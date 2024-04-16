- {{renderer :tocgen2}}
- [[#red]]==**Note: Copied From an .md file written on November 8,2023**==
- Note: The machine that is used here is running archlinux and is using yay as a third party package manager to be able to download AUR packages conviniently.
- $ yay -S libfido2 pam-u2f yubico-authenticator-bin
- Add the key 
  
    ```
    $ mkdir ~/.config/Yubico
    $ pamu2fcfg -o pam://hostname -i pam://hostname > ~/.config/Yubico/u2f_keys
  ```
  
  `hostname` should be changed.
- ## For sudo
	- Add the following line to `/etc/pam.d/sudo` as the first line.
	  
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
	  
	    This would still require the password. Changing `required` into `sufficient` from the line and moving it as the first line in the `auth` parts would make the hardware key as sufficient unlocking tool with the password as fallback. The problem with this is that upon loggin in, Gnome Keyring would then ask for the password phrase. To remove it, change the Gnome Keyring password into blank. It can be done using `seahorse` (installable using the package manager). This theoretically makes your password and data less secure. My root and home partitions are encrypted though and for my use case I prefer a little bit of convenience over security. In the future, I will be dabbling in using the key to also unlock Gnome Keyring.
	  
	    
	  
	    Relevant Links: https://221b.uk/gnome-login-using-u2f-security-tokens
	  
	                    https://askubuntu.com/questions/1167691/passwordless-login-with-yubikey-5-nfc
	  
	                    https://github.com/recolic/gnome-keyring-yubikey-unlock
- ## For Browsers
	- Open the Yubico Authenticator Program with the key inserted on the device. Add a PIN to be assigned to the key. The key is now usable for websites that supports it.
- ## Relevant Links:
  
  https://wiki.archlinux.org/title/Universal_2nd_Factor
  
  https://www.reddit.com/r/Fedora/comments/akck9m/authenticating_with_gdm_and_sudo_with_a_u2f/