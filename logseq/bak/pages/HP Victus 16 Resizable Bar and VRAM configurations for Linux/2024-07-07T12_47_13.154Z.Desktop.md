- Checking for Dynamic VRAM and BAR values within linux using
	- Following ways
		- ```
		  lspci -v | grep -i 'memory at'
		  ```
		- ```
		  sudo dmesg | grep BAR
		  ```
		- and within Ryzen Controller
	- yields values that would correspond to the system not having ReBAR or Dynamic RAM. When checking with windows (through ryzen controller) the system uses dynamic vram.
	-
- I tried to enable the ReBAR and 4G decoding options from the bios. Since the Advanced Settings are inaccessible from the F.19RevA BIOS version, I used Smokeless_UMAF from [[Advanced BIOS Settings for HP Victus 16]] to access options supposedly only accessed from the advanced settings.
- From a [kolAhilft.de Article](https://kolahilft.de/2023/09/vram-and-memory-for-integrated-graphics-with-linux/) , Linux works in a way (through its drivers) such that it allocates memory to the igpu according to the amount of RAM available. This does not cover the dedicated gpu and I still have no idea if ReBAR is working for the dGPU for the HP Victus 16.
-