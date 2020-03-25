# Power isolator board
<div class="cust_sheet" markdown="1">
<p class="cust_sheet-title" markdown="1"><strong>Default Alias:</strong> N/A</p>
<p class="cust_sheet-title" markdown="1"><strong>Type:</strong> N/A</p>
<p class="cust_sheet-title" markdown="1"><strong>Number of module(s):</strong> 0</p>
<p class="cust_sheet-title" markdown="1"><strong>Image</strong></p>
<p class="cust_indent" markdown="1"><img height="150" src="{{img_path}}/power-isolator-module.png"></p>
<p class="cust_sheet-title" markdown="1"><strong>Category(ies)</strong></p>
<p class="cust_indent" markdown="1">
<img height="50" src="{{img_path}}/sticker-power.png" title="Power">
</p>
</div>

## Power isolator board

The Power isolator board allows to manage multiple voltages into the same Luos network. As this board is not active, you can't detect it in your network.
This board isolates the voltage between each plugged-in side. You can use it to link components with different voltage needs. For example, to connect an XL320 motor (7V) and a MX28 motor (12V) on the same network, you can use a Dynamixel V2 board with a 7V power module for XL320, a Dynamixel V1 board with a 12V power module, and a Power isolator board to link both side together. You can connect a [Gate](gate.md) in the side you want without any functioning trouble.

<img height="350" src="{{img_path}}/power_isolator_example.png" alt="Power isolator example image">

<div class="cust_edit_page"><a href="https://{{gh_path}}{{boards_path}}/power-isolator.md">Edit this page</a></div>

