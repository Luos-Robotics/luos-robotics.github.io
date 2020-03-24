---
layout: post
title:  "Dynamixel"
date:   2019-03-15 17:59:00 +0100
categories: -_boards_list
tags: [sensor, interface]
---
{% assign board = "Dynamixel" %}
{% assign alias = "dxl_*id*" %}
{% assign type = "N x [Dynamixel motor](/../modules_list/dxl)" %}
{% include var.md %}

# How to start with the {{ board }} board
{% include card.md %}

## Versions of {{ board }} board

There are two versions of this board. One is the version for XL320 Dynamixel, the other is for other types of Dynamixel (eg. AX12). Both boards have a different connector.

![Dynamixel connectors]({{ "/" | absolute_url }}/assets/img/dxl-1.png)

Except for this connection, both versions work exactly the same way.

## How to connect and start the motors to the board

![Dynamixel]({{ "/" | absolute_url }}/assets/img/dxl-mod-1.jpg)

The Dynamixel board is special because it has a dynamic number of visible modules, depending on the number of motors plugged to it. If you have 5 motors on your board, you will see 5 DynamixelMotor modules.

> **Note:** If you don’t plug any motor to the board, it will create a special module called `void_dxl`.

This board creates modules dynamically upon motor detection. So in order to create modules, this board has to detect motors, and to detect them each motor needs to have a proper power supply.

Indeed, if you power the Luos network with an unadapted voltage, the motors won’t reply to the board requests and you won’t be able to see any Dynamixel module on your network.

To be detected, the Dynamixel motors need to use a baudrate of 1 000 000 `baud` and to have an ID between 1 and 30.

When your Dynamixel motors are properly configured, you can connect them to the Luos network. Be sure to respect the following order to have a proper start-up:

1. Connect the {{board}} board to the Luos network and to the Dynamixel motors.
2. Connect the correct power supply to the luos network.
3. Connect the USB board to your computer.
4. Wait for the blue LED at the back of the board to turn off.

> **Note:** The blue LED is ON when the network is busy detecting Dynamixel motors.

In order to begin using this board, you must disable the compliant mode, and you can then use the functions and variables of the [Dynamixel module](/../modules_list/dxl).

<blockquote class="warning"><strong>Warning:</strong> Dynamixel boards don’t belong to the power category. Thus, do not power your motors on the Robotis side, you won’t be able to share this power with others boards.</blockquote><br />
