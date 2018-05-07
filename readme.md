# Meteobike

"Meteobike" is a educational Raspberry Pi Zero Project at the University of Freiburg, Chair of Environmental Meteorology. In our course as part of the minor in meteorology and climatology we develop a system to measure, analyze and visualize the urban heat island of Freiburg. We measure temperature and humidity transcects and tag measurement locations with GPS. The system is battery operated and light, so it can be mounted on bikes. Communication with the Raspberry Pi Zero is enabled via wireless network.

![IMG_meteobike](IMG_meteobike.jpg)

## Overview

The mobile system is assembled using the following components:

![IMG_components](IMG_components.jpg)

## Preparing the Raspberry Pi Zero

### Connecting and starting the Raspberry Pi Zero

The Raspberry Pi Zero W is a microcomputer running a full operating system and providing input and output connectivity through a number of interfaces:

![IMG_raspberrypizerow](IMG_raspberrypizerow.jpg)

Your Raspberry Pi Zero W comes with a micro SD cards with the operating sysetm called [Raspbian](https://en.wikipedia.org/wiki/Raspbian Raspbian) preinstalled. The micro SD is inside the larger SD card adapter. Pull it out and insert it carefully into the card slot.

![IMG_sdcard](IMG_sdcard.jpg)

The first time you set-up your Raspberry Pi Zero W you will need a few other components, which can be used for setting-up all systems one after another. This includes a screen, a keyboard and a mouse, and connection cables. 

Later, once the system is assigned to your wireless networks, you can connect to and operate it though RealVNC, so there is no need for a phsyical keyboard, a mouse or a screen during its operation. All can be controlled though your smartphone.

The connection cables and supplies you need are as follows (not shown is the screen):

![IMG_setup_components](IMG_setup_components.jpg)

To connect the screen during the initial set-up, connect the mini-HDMI cable to you screen (possibly using a HDMI to VGA adapter if your screen does not support HDMI and only VGA):

![IMG_hdmi](IMG_hdmi.jpg)

Next, connect the USB ports. Your Raspberry Pi Zero W hast two USB slots, one (left) is for the USB devices (mouse, keyboard), one (right) is for supplying power. First connect to the USB devices. Because there is only one Port, but you need to connect two devices, you must also add initially a USB HUB. Here is the set-up:

![IMG_usbhub](IMG_usbhub.jpg)

Finally connect the power supply to the right mini USB connector. The Raspberry Pi Zero W now starts up, the green inicator light begins to flash, and instructions follow on your screen.

### Setting-up the wireless network

Follow the instructions on-screen to set-up your Raspberry Pi Zero W. It will automatically reboot after resizing. 

Then connect to the wireless network. Click in the menu-bar on the wireless network icon, select our network and enter the password. Details on the password and network can be found on your desk. Hover with the mouse over the network icon to read the IP number. Note the IP number on your sheet on the desk.

![SCS_network](SCS_network.jpg)

Next, localize the Raspberry Pi Zero W to your language and region. Go to 



## Installing the Sensors

### Installing the DHT 22 Sensor

Enter the following commands into the command line on the Raspberry Pi Zero to install the communication with the Adafruit DHT 22 library:

    $ sudo apt-get update
    $ sudo apt-get install build-essential python-dev python-openssl git
    $ git clone https://github.com/adafruit/Adafruit_Python_DHT.git
    $ cd Adafruit_Python_DHT
    $ sudo python setup.py install

Turn off the Raspberry Pi Zero. Disconnect the power cable, the USB cable, and the HDMI cables from the Raspberry Pi Zero. Connect the DHT22 sensor physically using the pre-soldered wires, with the following color coding on the pins of the Raspberry Pi Zero:

| DHT22 T/RH Sensor | Cable Color | Raspberry Pi Zero |
| ------------------ | ----------- | ----------------- |
| PIN 1  | <span style="color: red">Red Cable</span>  | PIN 1 (3V+)
| PIN 2 | <span style="color: orange">Orange Cable</span>  | PIN 7 (GPIO4)
| PIN 3 | (no cable)  |
| PIN 4 | <span style="color: brown">Brown Cable</span>  | PIN 9 (Ground)

Double check if the connection is correct. Then reconnect the USB cable, the HDMI cables, and finally the power cable to the Raspberry Pi Zero. The Raspberry Pi Zero restarts, and the green light flashes.

Once started, test the DHT 22 Sensor with the following commands in Python. First start the Phython environment for Python 2.7 in interactive mode. In Python, enter

    >>> import Adafruit_DHT
    >>> humidity, temperature = Adafruit_DHT.read_retry(Adafruit_DHT.DHT22,4)
    >>> print temperature, humidity
  
This will display the currently measured values.

Next, as an exercise you can calculate the vapour pressure using the Clausius-Clapeyron equation. First calculate the saturation vapour pressure in kPa, then convert RH to vapour pressure. Note that temperature needs to be entered in Kelvins.

    >>> import numpy 
    >>> saturation_vappress = 0.6113 * numpy.exp((2501000.0/461.5)*((1.0/273.15)-(1.0/(temperature+273.15))))
    >>> vappress=(humidity/100.0)*saturation_vappress
    >>> print vappress

## Installing the GPS Module

    $ sudo apt-get install gpsd gpsd-clients python-gps
    
    $ sudo systemctl stop serial-getty@ttyS0.service 
    $ sudo systemctl disable serial-getty@ttyS0.service

For the Raspberry Pi Zero we need to enable the serial port on the GPIO pins. Open the "nano" text editor and change the file `config.txt`
    
    $ sudo nano /boot/config.txt
    
Scroll to the the very bottom of the file with the arrow keys an add this on a new line:
    
    enable_uart=1
    
Save and Exit with Strng-O and Strng-X and reboot the Raspberry Pi Zero

Run these commands to use the serial port:
    
    $ sudo killall gpsd 
    $ sudo gpsd /dev/ttyS0 -F /var/run/gpsd.sock

An insert at the very end, but above the line `exit 0` the following command:

    gpsd /dev/ttyS0 -F /var/run/gpsd.sock

This enables that every time the Raspberry Pi Zero is bootet, the command will be executed. You can test the GPS using:

    $ cgps -s
