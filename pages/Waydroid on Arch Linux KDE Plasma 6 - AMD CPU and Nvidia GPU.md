# Prerequisites
	- Kernel with the necessary binder modules
		- Can use DKMS modules.
	-
- # Installation
	- Install `waydroid` package from AUR.
	- init Waydroid
		- without GAPPS
		  ```
		  # waydroid init
		  ```
		- with GAPPS
		  ````
		  # waydroid init -s GAPPS
		  ```
	- Enable service
		- ```
		  sudo systemctl enable --now waydroid-container.service
		  ```
	-
- # Installing Libndk/Libhoudini for ARM apps
	- ```
	  git clone https://github.com/casualsnek/waydroid_script
	  cd waydroid_script
	  python3 -m venv venv
	  venv/bin/pip install -r requirements.txt
	  sudo venv/bin/python3 main.py
	  ```
	-