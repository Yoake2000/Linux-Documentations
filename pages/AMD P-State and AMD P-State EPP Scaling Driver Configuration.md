# Prerequisites
	- Linux uses `acpi-cpufreq` or `amd-pstate` as the default scaling drivers for CPU. As of the writing  of this guide, `amd-pstate` is widely used by different distros for AMD processors. Configuring the scaling driver to use different drivers for different power states can improve Power and Performance efficiency as well as manage temperatures.
	- `amd_pstate` has three operation modes.
	- To enable ``amd_pstate_epp`` scaling driver for AMD Ryzen Processors, the processor needs to be from `zen 2` and newer generations.
	- To check if your device is able to use the scaling driver, `cpcc` support must be checked first using `lscpu | grep cppc`. The output must show `cpcc`. If it didn't, there may be a BIOS/UEFI setting needed to be changed first.
		-