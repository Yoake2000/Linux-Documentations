# Prerequisites
	- Linux uses `acpi-cpufreq` or `amd-pstate` as the default scaling drivers for the CPU. As of the writing of this guide, `amd-pstate` is widely used by different distros for AMD processors. Configuring the scaling driver to use different drivers for different power states can improve power and performance efficiency as well as manage temperatures.
	- [`amd_pstate` has three operation modes](https://wiki.archlinux.org/title/CPU_frequency_scaling#amd_pstate):`active`,`passive` and `guided` . From a [phoronix article testing the amd_pstate 'passive' and 'active' mode along with `acpi-freq` performance](https://www.phoronix.com/review/amd-pstate-epp-ryzen-mobile), it is evident that `active` mode implemented by  `amd_pstate_epp` driver performs the best considering power consumption, cpu temperature and  computing performance. ~~So far I haven't found any testing that includes the `guided` mode, as such this guide only considers the `active` mode only.~~ There are discussions on the [Phoronix forums](https://www.phoronix.com/forums/forum/hardware/processors-memory/1394900-linux-6-5-now-defaults-to-amd-p-state-active-epp-for-modern-ryzen-systems/page2) regarding the `guided` mode but there is still no definitive testing regarding its performance.
	- The AMD P-state `active` mode is the default now for Ryzen Linux Systems as of [Linux 6.5](https://www.phoronix.com/news/Linux-6.5-Power-Management).
	- To enable ``amd_pstate_epp`` (AMD P-state `active` mode) scaling driver for AMD Ryzen Processors, the processor needs to be from `zen 2` and newer generations.
	- To check if your device is able to use the scaling driver, `cpcc` support must be checked first using `lscpu | grep cppc`. The output must show `cpcc`. If it didn't, there may be a BIOS/UEFI setting needed to be changed first.
	- For more info about `amd-pstate` and `cpcc`, there is the [kernel documentation.](https://docs.kernel.org/admin-guide/pm/amd-pstate.html)
	- This guide discusses how to enable `amd_pstate_epp` driver or the AMD P-state `active` mode and configure the governor and EPP hints using `auto-cpufreq` depending on what power source is being used. This is applicable for mobile Ryzen systems (Laptops, etc) in order to manage battery life and device temperatures.
- # Enabling the P-State EPP Scaling Driver
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
- # Configuring Auto-Cpufreq to adjusts CPU governor and EPP hints automatically
	- The [`amd_pstate_epp` scaling driver offers only two pseudo-governors](https://wiki.archlinux.org/title/CPU_frequency_scaling#Autonomous_frequency_scaling), `powersave` and `performance`.  They're similar with other scaling driver governors but work differently in that these governors need EPP hint to be passed unto the CPU internal governor.
		- To check the current scaling driver governor, the following command can be used:
			- ```
			  cat /sys/devices/system/cpu/cpu*/cpufreq/scaling_governor
			  ```
	- The available EPP hints for the governors can be checked using:
		- ```
		  cat /sys/devices/system/cpu/cpu0/cpufreq/energy_performance_available_preferences
		  ```
	- Mixing and matching the governors and EPP hints can yield different results (refer to the aforementioned )