- Yubikey Security Key must first be added as a key for authentication as outline in [Enabling Authentication in GDM,sudo and Passkey in browsers using Yubikey Security Key](((661dd702-b5fa-4e99-8259-534692b87edf))) . Optionally add it as a key for sudo. This page would outline the steps to be done for using the Yubikey Security Key (Yubikey Security Key C NFC) for SDDM login and lockscreen.
- ## Use as a second factor authentication (2FA)
	- A useful [github repo](https://github.com/Zer0CoolX/Fedora-KDE-Yubikey-U2F-2FA-Logins-Guide) outlined the steps for using a Yubikey Security Key as 2FA for Fedora KDE Linux. These steps also works for Arch Linux but replacing some steps for the distro specific programs.
		- There are steps for using Yubikey 2FA for `sudo`,`su`,SDDM login screen, KDE Lockscreen and PolicyKit KDE Agent within the repo.
		- For `sudo`, the steps are similar from [Enabling Authentication in GDM,sudo and Passkey in browsers using Yubikey Security Key](((661dd702-e1c2-4803-9b49-7ff141bd8e72))) . Yubikey can also be a sufficient key if `sufficient` parameter is used instead of `required`.
		- A line in the repo instructions about changing ``auth include system-auth`` to `auth substack system-auth`. I didn't changed the line and haven't tested what are its effects. I don't know how `substack` or `include` affects the authentication.
		  collapsed:: true
			- The following links may contain the answers and I still yet to read them:
			  collapsed:: true
				- https://utcc.utoronto.ca/~cks/space/blog/linux/PAMStackingAndStopping
				- https://www.redhat.com/sysadmin/pam-configuration-file
				- https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system-level_authentication_guide/pam_configuration_files
				- https://www.linux.org/docs/man5/system-auth.html
	- I didn't used the instructions from this repo since I want to use the Yubikey as a sufficient key to login for my computer. For `sudo` and changing `required` to `sufficient`, this works adequately but for the SDDM login screen, even after changing to `sufficient`, a password is still required. For the lockscreen, the instructions and changing to `sufficient` is adequate though.
- #
- ## Adding YubiKey as a Global (System Wide) Authentication key
	- Adding the line (similar line used from the previous steps) `auth    sufficient  pam_u2f.so cue origin=pam://hostname appid=pam://hostname` as a first line in ``/etc/pam.d/system-auth`` would make the Yubikey a sufficient key for SDDM login. This would also include the KDE lockscreen, ``sudo``, and KDE PolicyKit Agent popup screens. Not yet tested if this works for `tty` screens. The `nouserok` flag is maybe needed after `cue` (or maybe replace it since `cue` does not do anything during prompts). So far, I did not used and there were no problems as far as I am concerned. Meanwhile information about the flag is on [Enabling Authentication in GDM,sudo and Passkey in browsers using Yubikey Security Key](((661df029-bbd1-4f3e-bf00-005aa8c2dc69))).
	- This [website article](https://lucaweiss.eu/post/2016-07-23-sddm-login-with-yubikey/) is the reference for this step but it uses ``yubico-pam`` instead of ``pam-u2f``.
-