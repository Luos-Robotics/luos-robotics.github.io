# Power Pi board
<div class="cust_sheet" markdown="1">
<p class="cust_sheet-title" markdown="1"><strong>Default Alias:</strong> gate</p>
<p class="cust_sheet-title" markdown="1"><strong>Type:</strong> <a href="/_pages/modules/modules_list/gate.md">Gate</a></p>
<p class="cust_sheet-title" markdown="1"><strong>Number of module(s):</strong> 1</p>
<p class="cust_sheet-title" markdown="1"><strong>Image</strong></p>
<p class="cust_indent" markdown="1"><img height="150" src="{{img_path}}/power-pi-module.png"></p>
<p class="cust_sheet-title" markdown="1"><strong>Category(ies)</strong></p>
<p class="cust_indent" markdown="1">
<img height="50" src="{{img_path}}/sticker-communication.png" title="Comunication">
<img height="50" src="{{img_path}}/sticker-power.png" title="Power">
<img height="50" src="{{img_path}}/sticker-cognition.png" title="Cognition">
</p>
</div>

## Power Pi board
The Power Pi board links any Raspberry Pi-like board to a Luos network. This board allows you to convert the Luos network power into 5V and up to 2A in order to power your Raspberry Pi or Odroid without any other power source. This board hosts a Gate module allowing to your Raspberry Pi to control your entire Luos network using Pyluos or any other language as a <a href="https://en.wikipedia.org/wiki/Single_system_image" target="_blank">single system image</a>.
The Power Pi board supports 5V to 24V DC input.

## Connection of Power Pi board to a Raspberry Pi
The connection of the Power Pi board to an ODrive board or to a Raspberry Pi board is made according to the following images.

> **Warning:** Be sure to plug the board on the right pins of the Raspberry Pi, and facing the right side. A bad connection may damage both boards.

![Plug location]({{img_path}}/power-pi-1.png)<br />
*Red rectangles show where to plug the Power Pi board on an ODrive board (left) and on a Raspberry Pi board (right).*

![Preview]({{img_path}}/power-pi-2.png)<br />
*On the left, a Power Pi board connected to an ODrive board; on the right, a Power Pi board connected to a Raspberry Pi board.*

## How to easily start to create your code using this board

Coding on a Raspberry Pi in a device can be quite boring. Generally, you can't connect any screen and keyboard to work properly.
That's why we created a small piece of code allowing to convert the Gate module stream into Web Socket messages.
By using this Web Socket, you can connect pyluos or any other lib you created to your Raspberry Pi. This way you can create and execute your device's behaviors directly on your computer.
When your behavior is complete and tested on your device, you just have to copy it into your Raspberry Pi to obtain an autonomous device.

To setup this pipe on your Raspberry Pi, please <a href="https://community.luos.io/t/create-a-web-socket-pipe-to-luos-network-using-raspberry-pi/197" target="_blank">follow the tutorial on our forum</a>.


## How to setup your Power Pi's Gate
You need to setup the Power Pi board before to start using it.

First, you have to connect your Raspberry Pi to the Gate network.

Several solution exist to configure the Raspberry Pi’s Gate, we provide you with two of them according to your setup:

### First solution: with a computer and the Raspberry Pi’s SD card
You will need the following parts:

 - A micro SD to SD card adapter
 - A computer with *Bonjour* installed on it
You can download *Bonjour* by Apple [here](https://support.apple.com/kb/DL999). Install it on your computer if you don’t already have it.

Plug the micro SD card to the micro-SD-to-SD adapter, and plug it to your computer. Ignore the messages that ask you if you want to format, and locate the SD card directory, named Boot. In Windows, it appears as a drive; in MacOS or Linux, go to

```bash
cd /Volumes/boot
```

Create a new file in this directory called `wpa_supplicant.conf`. The file should contain the following code:

```bash
country=fr
update_config=1
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev

network={
 scan_ssid=1
 ssid="SSID-Internet-box"
 psk="Secured-key"
}
```

Choose the country according to where you live, and replace `SSID-Internet-box` by the SSID of your internet device, and `Secured-key` by the password.

Save the file and eject the SD card. Replace it into the Raspberry Pi’s slot.

The Raspberry Pi can be located with the expression `raspberrypi.local`, thanks to the software *Bonjour*.

### Second solution: with a screen and a keyboard
![Raspberry-Pi connection]({{img_path}}/rpi-setup.jpg)

In order to establish a connection, you will need:

<ul>
<li>A QWERTY keyboard</li>
<li>An HDMI screen</li>
<li>An USB charger or USB to micro-USB cable to power up the Raspberry Pi</li>
<li>A micro USB to female USB adapter</li>
<li>A micro-HDMI to HDMI adapter</li>
<li>You can find the adapters you need in the Raspberry Pi zero toolkit, for example.</li>
</ul>

Plug all these elements and the Power Pi board on your Raspberry Pi (do not plug anything to the Power Pi board), and power it up. You should see on the screen the boot sequence and the file system expanding. After a few seconds, you should have a prompt asking you for username and password.

Usually, the Raspberry Pi has the default Raspberry username and password:

```bash
Username: pi
Password: raspberry
```

As you can see at the bottom of the boot screen, the SSH port is now open, so you should start by changing the password of your board to avoid any security issue.

To do that, use the following command:

```bash
sudo raspi-config
```

Choose option 1 to change your password and hostname, and choose option 2 to connect your board to your wifi.

You can check your Gate connection and retrieve the IP address using

```bash
ifconfig
```

and halt your system using

```bash
sudo halt
```

Your raspberry is now ready to be used, you can start setting your Luos network up.

> **Warning:** The Power Pi board doesn’t belong to the Power category. Using the power input of your Raspberry Pi doesn’t allow you to supply the others boards in the Luos network. In order to make it work properly, please use a power board on your system.

## How to use your Power Pi board

### Power
Please note that the Power Pi board connected to the Raspberry Pi is already powered by the Luos network, through the power boards you use (Power board or Battery board).

However, **the USB board can’t power the Raspberry Pi board**, because several voltage transformations are applied along the network. You can also use an universal power supply (+5.1V micro USB) directly plugged to the Raspberry Pi.

### Communication mode
By default, your Raspberry Pi starts a Luos service at boot called *pyluos-usb2ws*. This service creates a pipe between a **websocket** opened on **port 9342**, and the Luos system. If you send standard Luos Json data into this web socket, it's directly sent into the Luos network.

This way, you can control your device from your computer even if it is moving or dispatched. For example, if you are using pyluos to control your device, you can start your program with:

```python
from pyluos import Device
device = Device("raspberrypi.local")
device.modules
```

In this example, you can replace `raspberrypi.local` by your Raspberry Pi’s IP or hostname.

You should see the list of modules connected to the Power Pi board.


### Cognition mode
Also, you can use your Raspberry Pi like an embedded computer for your device.

To send your Json data to your network, please use the serial port `/dev/ttyAMA0` of your Raspberry Pi, as you can do it with the USB board.

<div class="cust_edit_page"><a href="https://{{gh_path}}{{boards_path}}/powerpi.md">Edit this page</a></div>

