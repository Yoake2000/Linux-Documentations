## RyzenAdj and Setting Power Limits
	- In order to help improve battery life on Linux, `ryzenadj` can be useful to limit the power drawn by the CPU.
	- Laptops automatically adjusts settings if it is plugged in or running in battery. As stated on the [RyzenAdj Github Wiki](https://github.com/FlyGoat/RyzenAdj/wiki/Renoir-Tuning-Guide), `power-saving ` or `max-performance` is automatically set depending on the power source and values vary depending on the device.
	- Setting much more lower power limits may help on improving the battery life. Thus I want to set the power limit max at 10W. Essentially limiting the CPU performance drastically in exchange for battery life. A [related reddit thread](https://www.reddit.com/r/AMDLaptops/comments/11jjmmc/turned_my_6800h_into_a_6800u_using_ryzenadj/) discusses this comparing it into changing their mobile CPU into its low power equivalent.
	- Setting power limits can be done on CLI using:
		- `# ryzenadj  --stapm-limit=10000 --fast-limit=10000 --slow-limit=10000 `
		- needing `sudo` to run
	- ### To_Improve
		- Possible references:
			- https://www.reddit.com/r/archlinux/comments/pwpk8o/in_my_laptop_linux_has_very_bad_battery_duration/
			-
- ## Automatically setting power limit using RyzenAdj on KDE
	- KDE is able to run commands or scripts when changing power profiles on System Settings > Energy Saving.
	- Running commands or scripts that require `sudo` using the previous step won't prompt to enter the password. If you have a yubikey set up like from [[YubiKey Security Key For KDE Plasma 6 SDDM and lockscreen login in Linux]], the Yubikey will light up and you would be able to authenticate for `sudo`.
	- In order to prompt for the password, the following bash script was used:
		- ```
		  #!/bin/bash
		  echo $(kdialog --password "sudo password required") | sudo ryzenadj --tctl-temp=75  --stapm-limit=10000 --fast-limit=10000 --slow-limit=10000
		  ```
		- This [Ask Ubuntu forum post](https://askubuntu.com/questions/1067064/how-do-i-run-a-graphical-sudo-in-bash-on-kubuntu-18-04-now-that-kdesudo-is-gone) discusses how to run Graphical Sudo Prompts in KDE. This is where the bash script part to prompt `sudo` password was referenced. Note this may be outdated and not very secure.
-