---
layout: post
title:  "Power Pi"
date:   2019-03-15 17:57:00 +0100
categories: -_boards_list
tags: [communication, cognition, power]
---
{% assign board = "Power Pi" %}
{% assign alias = "Gate" %}
{% assign type = "[Gate](/../modules_list/gate)" %}
{% include var.md %}

# How to start with the {{ board }} board
{% include card.md %}

## How to use {{ board }} board

The {{ board }} board links any Raspberry Pi-like board to a Luos network. This board allows you to convert the Luos network power into 5V and up to 2A in order to power your Raspberry Pi or Odroid without any other power source. This board hosts a Gate module allowing to your Raspberry Pi to control your entire Luos network using Pyluos or any other language as a [single system image](https://en.wikipedia.org/wiki/Single_system_image).
The {{ board }} board supports 5V to 24V DC input.

## Connection of {{ board }} board to a Raspberry Pi
The connection of the {{ board }} board to an ODrive board or to a Raspberry Pi board is made according to the following images.

<blockquote class="warning"><strong>Warning:</strong> Warning: Be sure to plug the board on the right pins of the Raspberry Pi, and facing the right side. A bad connection may damage both boards.</blockquote><br />

![Plug location]({{ "/" | absolute_url }}/assets/img/power-pi-1.png)<br />
*Red rectangles show where to plug the {{ board }} board on an ODrive board (left) and on a Raspberry Pi board (right).*

![Preview]({{ "/" | absolute_url }}/assets/img/power-pi-2.png)<br />
*On the left, a {{ board }} board connected to an ODrive board; on the right, a {{ board }} board connected to a Raspberry Pi board.*

## How to easily start to create your code using this board

Coding on a Raspberry Pi in a robot can be quite boring. Generally, you can't connect any screen and keyboard to work properly.
That's why we created a small piece of code allowing to convert the Gate module stream into Web Socket messages.
By using this Web Socket, you can connect pyluos or any other lib you created to your Raspberry Pi. This way you can create and execute your robot behavior directly on your computer.
When your behavior is complete and tested on your robot, you just have to copy it into your Raspberry Pi to obtain an autonomous robot.

To setup this pipe on your Raspberry Pi, please [follow the tutorial on our forum](https://forum.luos.io/t/create-a-web-socket-pipe-to-luos-network-using-raspberry-pi/197).


## How to setup your {{ board }}'s Gate
You need to setup the {{ board }} board before to start using it.

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
![Raspberry-Pi connection]({{ "/" | absolute_url }}/assets/img/rpi-setup.jpg)

In order to establish a connection, you will need:

<ul>
<li>A QWERTY keyboard</li>
<li>An HDMI screen</li>
<li>An USB charger or USB to micro-USB cable to power up the Raspberry Pi</li>
<li>A micro USB to female USB adapter</li>
<li>A micro-HDMI to HDMI adapter</li>
<li>You can find the adapters you need in the Raspberry Pi zero toolkit, for example.</li>
</ul>

Plug all these elements and the {{ board }} board on your Raspberry Pi (do not plug anything to the {{ board }} board), and power it up. You should see on the screen the boot sequence and the file system expanding. After a few seconds, you should have a prompt asking you for username and password.

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

> **Warning:** The {{ board }} board doesn’t belong to the Power category. Using the power input of your Raspberry Pi doesn’t allow you to supply the others boards in the Luos network. In order to make it work properly, please use a power board on your system.

## How to use your {{ board }} board

### Power
Please note that the {{ board }} board connected to the Raspberry Pi is already powered by the Luos network, through the power boards you use (Power board or Battery board).

However, **the USB board can’t power the Raspberry Pi board**, because several voltage transformations are applied along the network. You can also use an universal power supply (+5.1V micro USB) directly plugged to the Raspberry Pi.

### Communication mode
By default, your Raspberry Pi starts a Luos service at boot called *pyluos-usb2ws*. This service creates a pipe between a **websocket** opened on **port 9342**, and the Luos system. If you send standard Luos Json data into this web socket, it's directly sent into the Luos network.

This way, you can control your robot from your computer even if it is moving or dispatched. For example, if you are using pyluos to control your robot, you can start your program with:

```python
from pyluos import Robot
robot = Robot("raspberrypi.local")
robot.modules
```

In this example, you can replace `raspberrypi.local` by your Raspberry Pi’s IP or hostname.

You should see the list of modules connected to the {{ board }} board.


### Cognition mode
Also, you can use your Raspberry Pi like an embedded computer for your robot.

To send your Json data to your network, please use the serial port `/dev/ttyAMA0` of your Raspberry Pi, as you can do it with the USB board.
