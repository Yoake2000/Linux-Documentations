# Preliminary Ideas
	- As of 2024, Linux works most of the time on Nvidia GPUs. Drivers and support is improving with common devices/configurations/setups works right out of the box specially on bleeding edge distros.
	- This page would detail the quirks and tweaks done on my Arch Linux system on an HP Victus 16 Laptop with an AMD Ryzen 5 5600H and rocking an Nvidia Optimus System with Nvidia RTX 3050ti mobile. The system is also on KDE Plasma 6.0.1 as of the writing.
	- Some of the problems are not present when run on X.org rather than wayland such as some missing options on ``Nvidia Settings`` but it presents a whole lot of problems specially very laggy system and unstable windows with a lot of glitches. X.org is currently not supported by the KDE team as such only the wayland problems are outline here.
	- Most of the working solutions here are from [this github repo.](https://github.com/ventureoo/nvidia-tweaks?tab=readme-ov-file) It is also useful for NVIDIA users. The Arch Wiki ought to have been updated using the information from this repo.
- # Very choppy and low framerate on external display connected to HDMI (port wired to the dGPU)
	- When the external display is connected to the HDMI port of the laptop (which is directly wired to the dGPU), the output of the display is choppy/laggy and seems to have low frame rate. Even when adjusting the frame rate of the monitor, it will still persists.
	- This problem is not present when using Windows (I dual boot), and as such it is not a hardware problem but maybe a software one. It may have come from the proprietary Nvidia drivers and/or the compositor (kwin) of KDE plasma.
	- From the previously mentioned [github repo,](https://github.com/ventureoo/nvidia-tweaks?tab=readme-ov-file) the problem may have been from the compositor but the [commit](https://invent.kde.org/plasma/kwin/-/merge_requests/5115) that would have solved the issue was merged months ago. Running KDE Plasma 6.1, the issue still persists.
	- From the [https://bugs.kde.org/show_bug.cgi?id=452219](https://bugs.kde.org/show_bug.cgi?id=452219), this kernel parameter somewhat solves the problem:
		- ```
		  nvidia.NVreg_EnableGpuFirmware=0
		  ```
		- This disables the usage of the new firmware and so far I did not see any problems or glitches. This does not completely solves the problem though and it is very apparent when playing games. Framerates would not be as smooth.
	- There's also this environment variable (``/etc/environment`` for global variables):
		- ```
		  OGL_DEDICATED_HW_STATE_PER_CONTEXT=ENABLE_ROBUST
		  ```
		- that supposedly would solve the problem but I still haven't tried. From the experience of a user in the bug page, it didn't work for them though.
- # Power Limit of Nvidia RTX 3050ti is capped at 65W compared to Windows that is able to run at 75W
	- From my anecdotal experience, game performance on Windows and Arch linux (as of June 2024) is on par and maybe sometimes superior on the latter. Some games though run at lower framerates on Linux such as Counter Strike 2.
		- Regarding Counter Strike 2, performance on the current (as of writing) release version of the NVIDIA and with default configurations was abysmal compared to windows with most of the time hovering at 60 fps with a lot of stutters and spike compared to around 120 fps on windows. After using the beta drivers, the framerates have stabilized at around 100 fps without stutters.
		- All the framerate figures here are not from any rigorous tests but are only from numbers I see from the fps counters and guessing ballpark average values from them. The differences between the smoothness though are apparent which may have been reflected on my guessed figures.
	- After tweaking with the nvidia configurations, I was able to observe from `nvidia-smi` that the power usage of the dGPU was only capped at 60W at first. After digging a bit, I found out that `Dynamic Boost` would have to be used to increase the power limit of the dGPU.
		- Install the `nvidia-powerd` package, enable the systemd service and reboot to use Dynamic Boost.
		- ```
		  sudo systemctl enable --now nvidia-powerd.service
		  ```
	- This would only add 5W as a boost to the power limit of the dGPU though.
		- https://forums.developer.nvidia.com/t/the-default-power-limit-of-my-4060-laptop-halves-its-performance/294699/10
		- https://forums.developer.nvidia.com/t/dynamic-boost-issues-with-rtx-4090-mobile-on-linux/283605/10
		- https://forums.developer.nvidia.com/t/has-anyone-been-able-to-run-an-rtx-3060-laptop-gpu-at-more-than-80w-on-linux/192959/112?page=4
		- https://forums.developer.nvidia.com/t/power-limit-on-3000-mobile-series/193443
		- https://github.com/AdnanHodzic/auto-cpufreq/issues/714
		- https://www.reddit.com/r/linux_gaming/comments/1diwsnb/rtx_4070_laptop_gpu_wont_reach_maximum_tgp/
		- https://forums.developer.nvidia.com/t/has-anyone-been-able-to-run-an-rtx-3060-laptop-gpu-at-more-than-80w-on-linux/192959/111
	- Dynamica Boost - https://download.nvidia.com/XFree86/Linux-x86_64/535.54.03/README/dynamicboost.html