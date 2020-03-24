---
layout: post
title:  "Wire power input"
date:   2019-03-15 17:57:00 +0100
categories: -_boards_list
tags: [power]
---
{% assign board = "Wire power input" %}
{% assign alias = "" %}
{% assign type = "" %}
{% include var.md %}

# How to start with the {{ board }} board
{% include card.md %}

## How to use {{ board }} board

The {{ board }} board allows to power your Luos Network using a wire screw interface. This board is not active, you can't detect it.
The {{ board }} board can provide 5V to 24V DC.

You can manage multiple voltage in the same network following Luos power rules defined in [Luos boards general use]({{ "/../prototyping_boards/electronic-use" | absolute_url }}) or by using a [power isolator board]({{ "/../boards_list/power-isolator" | absolute_url }}).
