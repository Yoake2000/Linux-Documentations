# Motivations
	- When playing `Elden Ring` using `Proton-GE`, all audio would then produce crackling sound specially at higher volumes/pitch.
	- A lot were tried to fix the problem which includes changing the compatibility layer (proton versions) and turning off `gamemode` and `FSR`.
- # Fix
	- Changing the highest requested RTC interrupt frequency from the default 64 hz to 2048 fixes the problem. This can be done using the following
		- ```
		  # echo 2048 > /sys/class/rtc/rtc0/max_user_freq
		  # echo 2048 > /proc/sys/dev/hpet/max-user-freq
		  ```
		- and restarting the `pipewire` service by
		- ```
		   systemctl --user restart wireplumber pipewire pipewire-pulse  
		  ```
	- This fix would only be applicable until the device is powered off. Programs that uses  the pipewire service (specially the screen recording function) needs to be restarted.
	- To make this change persistent across boots, one can run the two first commands at boot by making a script and creating a `systemd` service as outline on the [Arch Wiki.](https://wiki.archlinux.org/title/Systemd/FAQ#How_can_I_make_a_script_start_during_the_boot_process?) . The other option is to use `systemd-tmpfiles-temporary files` as outline in [this Arch Wiki article.](https://wiki.archlinux.org/title/Systemd#systemd-tmpfiles_-_temporary_files)
		- For the first option, one can easily create a bash script and make a service that would run the service on boot.
		- For the second option, one can make two files on the directory `/etc/tmpfiles.d/` with a `.conf` file extension, each containing the following
			- ```
			  #    Path                  Mode UID  GID  Age Argument
			  w    /sys/class/rtc/rtc0/max_user_freq     -    -    -    -   2048
			  ```
			- ```
			  #    Path                  Mode UID  GID  Age Argument
			  w    /proc/sys/dev/hpet/max-user-freq     -    -    -    -   2048 
			  ```
- # Relevant Links
	- https://wiki.archlinux.org/title/Professional_audio
		- Contains the fix under the System Configuration section.
	- Discussions regarding similar problems which may or may not had mentioned the fix
		- https://www.reddit.com/r/linuxaudio/comments/blot4q/how_i_got_under_3_ms_with_a_p4/
		- https://linuxmusicians.com/viewtopic.php?t=25625
		- https://gist.github.com/ammuench/164ea026909eb256ba379f9e3a99af85
		- https://bbs.archlinux.org/viewtopic.php?id=284875
		- https://bbs.archlinux.org/viewtopic.php?id=283324
		-
	-