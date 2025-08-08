# digital_thermal_right_lcd
A program that displays temperature on the thermal right cpu cooler's digital screen for Linux.

# To build the controller :

## Create a python environement:
`python3 -m venv .venv`

## Source the environnement:
`source .venv/bin/activate`

## Install the requirements:
`pip install -r requirements.txt`

## Build as executable : 
`pyinstaller --onefile src/controller.py`

You may also launch it directcly with python

`python3 src/thermalright_digital_lcd/controller.py config.json`

# Set up as a service so it start at each startup: 
Create a file in /etc/systemd/system/digital_lcd_controller.service:
`sudo nano /etc/systemd/system/digital_lcd_controller.service`

Write this inside :
```
[Unit]
Description=Lcd screen controller
After=network.target udev.service systemd-modules-load.service

[Service]
ExecStart=/bin/bash -c 'source /path/to/venv/.venv/bin/activate && exec python /path/to/thermalright_digital_lcd/src/thermalright_digital_lcd/controller.py /path/to/thermalright_digital_lcd/config.json'
User=yourusername
Group=yourusername
Type=simple
Restart=always
RestartSec=5s


[Install]
WantedBy=multi-user.target
```
Then execute
```
sudo systemctl enable digital_lcd_controller
# check status
sudo systemctl status digital_lcd_controller
```

#  Modify the config with the UI :

`python3 src/led_display_ui.py config.json`

# Troubleshooting
If the libhidapi is missing :
ImportError: Unable to load any of the following libraries:libhidapi-hidraw.so libhidapi-hidraw.so.0 libhidapi-libusb.so libhidapi-libusb.so.0 libhidapi-iohidmanager.so libhidapi-iohidmanager.so.0 libhidapi.dylib hidapi.dll libhidapi-0.dll
Install it with :
`sudo apt update && sudo apt install libhidapi-hidraw0 libhidapi-libusb0`

If the device can't be found : 
"Error initializing HID device: unable to open device No device found, with VENDOR_ID: 1046, PRODUCT_ID: 32769"
Try running the controller as root : 
`sudo python3 src/controller.py config.json`
The correct way to fix this problem is to create a udev rule by editing this file "/etc/udev/rules.d/99-hid-device.rules" : 
`sudo nano /etc/udev/rules.d/99-hid-device.rules`
and paste this line : 
`SUBSYSTEM=="hidraw", ATTRS{idVendor}=="0416", ATTRS{idProduct}=="8001", MODE="0666"`
