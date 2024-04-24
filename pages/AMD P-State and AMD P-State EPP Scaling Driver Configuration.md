# Requirements
	- Linux
	- To enable ``amd_pstate_epp`` scaling driver for AMD Ryzen Processors, the processor needs to be from `zen 2` and newer generations.
	- To check if your device is able to use the scaling driver, `cpcc` support must be checked first using `lscpu | grep cppc`. The output must show `cpcc`. If it didn't, there may be a BIOS/UEFI setting needed to be changed first.
		-