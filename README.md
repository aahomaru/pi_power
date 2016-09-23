# Pi Power

**As of 09/23/2016 this is incomplete - I'm trying to get it finished but please don't go building a version of it until it's ready - please check back soon**

The [Raspberry Pi](https://www.raspberrypi.org/) is a low cost, single board computer with reasonable performance and relatively low power
consumption, typically a version of the Linux operating system, often [Raspbian](https://www.raspberrypi.org/downloads/raspbian/).

The Pi family of boards have turned out to be very useful machines for standalone and/or
portable projects such as remote environment monitoring, cameras, etc.

But to be truly portable, a system needs to include a power source and a way to control
that power - such as a rechargeable battery, a charger, an on/off switch and some
way to monitor battery status.

What I want is something equivalent to the way my iPhone works
- To power it up from a cold state, press a button for a few seconds
- To power it off, press the same button for a few seconds
- Indicate how much power remains in the battery
- Provide an alert when that is running really low
- Shut down safely without any data corruption if the battery does run out
- To recharge the battery, just plug in a cable from a USB charger


This project provides one approach to reaching this goal, building on the [LiPoPi](https://github.com/NeonHorizon/lipopi) project
from Daniel Bull.

# Overview

The system consists of some relatively simple circuitry that links the Pi with a LiPoly battery charger
and two Python scripts that handle monitoring and control.



The **hardware** looks like this:

![Power On / Power Off - schematic](/images/pi_power_schematic_1.png)


![Battery monitor ADC - schematic](/images/pi_power_schematic_2.png)


![Status LEDs - schematic](/images/pi_power_schematic_3.png)



The **software** consists of two python scripts:

[pi_power.py](pi_power.py) monitors the battery voltage and handles shutdown of the system
when the battery runs out, or when the user pushes a button. It writes the current battery status to a file.

[pi_power_leds.py](pi_power_leds.py) checks the status file and sets a red or green led according to that.


*Read on for details...*


# Hardware

The system uses a
[Adafruit PowerBoost 1000 Charger - Rechargeable 5V Lipo USB Boost @ 1A - 1000C](https://www.adafruit.com/products/2465)
to provide regulated 5V power from a LiPoly battery or a USB power supply. When a USB power supply is attached, the PowerBoost not
only powers the RasPi but also recharges the battery. It is a great little device for this sort of project. Adafruit sell it
for around $20 and they have a detailed [tutorial](https://learn.adafruit.com/adafruit-powerboost-1000c-load-share-usb-charge-boost) available.

There are three parts to the circuitry

## Power On / Power Off Switch

A momentary pushbutton switch is used to power up the Pi from a cold start and to trigger an orderly shutdown of the system.
This machinery is taken from the [LiPoPi](https://github.com/NeonHorizon/lipopi) project
from Daniel Bull, which I contributed to. Please check out that site for information on how that all works.

Note that it leaves out the low battery component of that project as we can monitor that as part of the
battery voltage in the next section.

![Power On / Power Off - schematic](/images/pi_power_schematic_1.png)


## Voltage Monitoring with an ADC

To assess how much power is left in a battery we can use the pins on the PowerBoost1000C. The Pi does not have an Analog to Digital Converter
itself so we need to add an external one in the form of a [MCP3008](https://www.adafruit.com/products/856), which is an 8-Channel 10-Bit ADC With SPI Interface.
We are only using two channels so this is a bit of an overkill, but they are not expensive (around $4).

The **USB** pin on the PowerBoost has the voltage of the input USB connection. When the cable is not connected this is 0 V and around 5.2V when connected.
Nominally, a USB power supply provides 5V but manufacturers often bump this up a little bit to counteract any voltage drop over
the supply cables.

The measured USB voltage is used to determine if the cable is attached or not. Doing this via an ADC is overkill but
as we need the ADC for the battery voltage, it makes sense to use it.


The Battery voltage on the **Bat** pin is what really tells us the current battery status. This has the output voltage of the LiPoly battery,
which varies from 3.7V when depleted to around 4.2V when fully charged.

The maximum input voltage for the analog side of the ADC is 3.3V so we need to reduce the PowerBoost voltages using a
pair of resistors acting as a [Voltage Divider](https://en.wikipedia.org/wiki/Voltage_divider). In this case a combination of 6.8K and 10K resistors
brings the maximum input voltages into the desired range.

The ADC uses the SPI interface on the RaspberryPi side which uses four GPIO pins. The code for this interface was written by the Adafruit
team and manages SPI in software, as opposed to the Pi's hardware SPI interface, so you can choose other GPIO pins if necessary.

![Battery monitor ADC - schematic](/images/pi_power_schematic_2.png)

Note that this is a schematic and does not correspond to the actual chip pinout.


## Battery Status LEDs

The system needs a way

![Status LEDs - schematic](/images/pi_power_schematic_3.png)


## Putting it all together

Wiring all this up on a breadboard gets you something like this:

![Breadboard](/images/pi_power_breadboard_small.png)

Here is a larger version of this [layout](https://github.com/craic/pi_power/images/pi_power_breadboard.png)

And here is what my actual breadboard looks like - not pretty, but functional

![Breadboard Photo](/images/breadboard_photo.jpg)


# Software

**to be added...**


# Deployment

**to be added...**




# Notes

Please take a look at the [Wiki](https://github.com/craic/pi_power/wiki/Pi-Power-Wiki) for more background.


The power on / power off machinery is taken from the [LiPoPi](https://github.com/NeonHorizon/lipopi) project from Daniel Bull, which I have contributed to.

The breadboard layout was created to make the circuit easy to understand (you may argue whether or not I succeeded in that!).
One consequence of that is that a number of the jumper wires are relatively long, compared to a compact circuit board.
This may have a couple of unwanted side effects.

Long wires attached to RasPi GPIO pins may act as radio aerials. Even though the code uses internal pull-down resistors to avoid the pins from floating,
I did have a problem with the shutdown pin (GPIO26 in the circuit) getting triggered when I plugged the PowerBoost 1000C input USB
cable back in, after it had been disconnected.
I solved that by placing a 0.1uF ceramic capacitor between GPIO26 and Ground.

In addition, there can be voltage drop over longer wires and the breadboard tracks. The Adafruit guide to the PowerBoost 1000C mentions this. Shorter wires are better.


Please do not just wire your circuit from the breadboard diagram - understand the circuit first - you may be able to come up with a neater layout.
More importantly, I may have made a mistake in creating the diagram.

Use raspiconfig for older Pis...

Add the fix for Pi 3 etc...

Warning about systemd etc...





