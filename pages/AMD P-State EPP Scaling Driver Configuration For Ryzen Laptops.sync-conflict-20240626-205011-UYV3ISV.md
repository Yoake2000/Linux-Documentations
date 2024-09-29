## Prerequisites
	- Almost everything in this page may come pre-configured on newer laptops (and maybe even desktop CPUs) after installation of Arch Linux (No Sources). If not and/or you just want to manually configure the options, this page may come in handy.
	  Linux uses `acpi-cpufreq` or `amd-pstate` as the default scaling drivers for the CPU. As of the writing of this guide, `amd-pstate` is widely used by different distros for AMD processors. Configuring the scaling driver to use different drivers for different power states can improve power and performance efficiency as well as manage temperatures.
	- [`amd_pstate` has three operation modes](https://wiki.archlinux.org/title/CPU_frequency_scaling#amd_pstate):`active`,`passive` and `guided` . From a [phoronix article testing the amd_pstate 'passive' and 'active' mode along with `acpi-freq` performance](https://www.phoronix.com/review/amd-pstate-epp-ryzen-mobile), it is evident that `active` mode implemented by  `amd_pstate_epp` driver performs the best considering power consumption, cpu temperature and  computing performance. ~~So far I haven't found any testing that includes the `guided` mode, as such this guide only considers the `active` mode only.~~ There are discussions on the [Phoronix forums](https://www.phoronix.com/forums/forum/hardware/processors-memory/1394900-linux-6-5-now-defaults-to-amd-p-state-active-epp-for-modern-ryzen-systems/page2) regarding the `guided` mode but there is still no definitive testing regarding its performance.
	- The AMD P-state `active` mode is the default now for Ryzen Linux Systems as of [Linux 6.5](https://www.phoronix.com/news/Linux-6.5-Power-Management).
	- To enable ``amd_pstate_epp`` (AMD P-state `active` mode) scaling driver for AMD Ryzen Processors, the processor needs to be from `zen 2` and newer generations.
	- To check if your device is able to use the scaling driver, `cpcc` support must be checked first using `lscpu | grep cppc`. The output must show `cpcc`. If it didn't, there may be a BIOS/UEFI setting needed to be changed first.
	- For more info about `amd-pstate` and `cpcc`, consult the [kernel documentation.](https://docs.kernel.org/admin-guide/pm/amd-pstate.html)
	- This guide discusses how to enable `amd_pstate_epp` driver or the AMD P-state `active` mode and configure the governor and EPP hints using `auto-cpufreq` under the Arch Linux distro depending on what power source is being used. This is applicable for mobile Ryzen systems (Laptops, etc) in order to manage battery life and device temperatures.
- ## Enabling the P-State EPP Scaling Driver
	- To enable the `amd_pstate_epp` driver, the kernel module parameter `amd_pstate=active` must first be added. This may be unnecessary for newer Linux kernels.
		- For Arch Linux systems using the `refind` boot manager, the kernel parameter can be added on `/boot/refind_linux.conf` or `/boot/EFI/refind/refind.conf` depending on how the boot manager was [configured.](https://wiki.archlinux.org/title/REFInd#Configuration) .
		- For other distros and/or boot managers, refer to their respective guides on how to change kernel parameters.
		- The ``active`` mode is only available for kernel versions 6.3 and higher.
	- If `guided` or `passive` is the preferred mode, just change the kernel parameter to be added into `amd_pstate=guided` or `amd_pstate=passive` respectively.
		- The ``guided`` mode is only available for kernel versions 6.4 and higher.
	- To verify if the `amd_pstate_epp` driver is being used, run the following command:
		- ````
		  cat /sys/devices/system/cpu/cpu0/cpufreq/scaling_driver
		  ```
- # (Currently not Used) Configuring Auto-Cpufreq to adjusts CPU governor and EPP hints automatically
	- Currently using the `power-profiles-daemon` and KDE Plasma Power Profiles to automatically control the CPU power profiles. It basically works the same way but is integrated to the KDE Plasma Desktop Environment.
	- ~~The [`amd_pstate_epp` scaling driver offers only two pseudo-governors](https://wiki.archlinux.org/title/CPU_frequency_scaling#Autonomous_frequency_scaling), `powersave` and `performance`.  They're similar with other scaling driver governors but work differently in that these governors need EPP hint to be passed unto the CPU internal governor.~~
		- ~~To check the current scaling driver governor, the following command can be used:~~
		- ```
		  cat /sys/devices/system/cpu/cpu*/cpufreq/scaling_governor
		  ```
	- ~~The available EPP hints for the governors can be checked using:~~
		- ```
		  cat /sys/devices/system/cpu/cpu0/cpufreq/energy_performance_available_preferences
		  ```
	- ~~Mixing and matching the governors and EPP hints can yield different results (refer to the aforementioned [Phoronix article](https://www.phoronix.com/review/amd-pstate-epp-ryzen-mobile) for benchmarks.) in performance.~~
	- ~~The program `auto-cpufreq` automatically adjusts CPU governors and frequencies and supports adjusting the EPP hints for `amd_pstate_epp` scaling driver depending on the power source. It is useful for managing and improving battery life of laptops.~~
		- ~~It can also be configured to use preferred CPU governors by making a config file `/etc/auto-cpufreq.conf`. An example and instructions on how to configure the config file is in the program's [github repo.](https://github.com/AdnanHodzic/auto-cpufreq)~~
	- ~~We only need to adjust the CPU governor and EPP hint for battery and AC power source. From the aforementioned Phoronix tests, `powersave balanced_power` and `powersave power` governor and EPP hint pairs are best suited for power consumption and cpu temperature with little impact on the computer's performance when using AC power and Battery power respectively.~~
	- ~~The following lines on the `/etc/auto-cpufreq.conf` should be changed in order to set the previous governor and EPP pairing:~~
		- ~~under the `[charger]` block the lines `governor = powersave` and `energy_performance_preference = balanced_performance` should be inserted/changed from default.~~
		- ~~under the `[battery]` block the lines `governor = powersave` and `energy_performance_preference = power` should be inserted/changed from default.~~
	- ~~To apply changes, the ``systemd`` service for `auto-cpufreq` can be restarted using:~~
		- ```
		  sudo systemctl restart auto-cpufreq.service
		  ```
- # Notes:
	- This guide is heavily inspired by a [Lemmy.world post](https://lemmy.world/post/3081149)that discusses on how to configure the AMD P-States EPP driver. This guide though tries to organize the information from the post regarding some terms and configurations with more sources and is focused on automatically adjusting the CPU governors and the EPP hints on Ryzen Laptops. This guide is also targeted for Arch Linux Systems since I'm using one.
	- Some forum discussions and guides were also consulted in creating this guide.
		- https://forum.endeavouros.com/t/dont-get-amd-p-state-active/41018
		- https://forum.level1techs.com/t/amd-p-state-driver/197885/89?page=2
		- https://www.reddit.com/r/linux/comments/1aoaps4/amd_pstate_epp_amd_pstate_power_management_energy/
		- https://forum.endeavouros.com/t/how-to-use-amd-p-state-in-linux/25247/425
		- https://bbs.archlinux.org/viewtopic.php?id=292940
		- https://www.reddit.com/r/framework/comments/18b9cp8/does_framework_have_cppc_enabled_for_linux/
		- https://gitlab.com/echoa/amd-pstate-guide
	- The following benchmarks deals with AMD P-state and may be useful:
		- https://community.frame.work/t/responded-linux-amd-pstate-scaling-driver-benchmarks/45830
		- https://www.phoronix.com/review/amd-pstate-first
		- https://www.phoronix.com/news/AMD-P-State-Guided-Auto (Not a benchmark but it talks about a benchmark performed by AMD regarding `guided` mode.
	- Instead of `auto-cpufreq`, [`auto-epp`](https://github.com/jothi-prasath/auto-epp) can also be used. [`power-profiles-daemon`](https://gitlab.freedesktop.org/upower/power-profiles-daemon) can also be used for KDE and GNOME desktops. Note that they may conflict with each other and other programs such as `tlp`. For a very minimalist but harder approach, `cron` can also be used.
	- `auto-cpufreq` may show the following "Warning: CPU turbo is not available" on its status. An [issue](https://github.com/AdnanHodzic/auto-cpufreq/issues/602) has already been filed in its github repo regarding it. It is a bug since turbo still works; maxing out the CPU will make it reach turbo frequencies.