# producer-mic
This mic was created to work with Shure ULX-D and Axient Digital wireless systems. The venue was using a custom Countryman B3 mic wired into a PTT radio surveillance kit. While simple, this design was fragile, difficult to repair, and caused clicks when the mic was turned on and off.

## Circuit Design
![shure mute diagram](img/mute_20switch.gif)

Shure sells inline mute switches, these are often used by NFL Refs to talk to America. A schematic is posted in their knowledge base. The additional caps and resistors in this circuit help clean up small voltage spikes when the switch changes state.

The B3 capsule in the previous iteration of the mic is expensive, especially when it is just used for comm! It was originally chosen for its size, but we can go much smaller!

### Microphone
![mic on a lego stud](img/lego.jpg)
Yes, that is a microphone on a lego stud! This is a Knowles SPH6611LR5H-1. This $0.92 component is constructed using processes similar to how other ICs are made. The diaphragm and amplifier are etched out of silicon. This particular component is bottom ported, the audio passes through the bottom of the PCB into the microphone.

![bottom port](img/mems.jpg)

### Switch
For a Push-To-Talk, the switch needs normally be closed to mute the audio. Pressing the button should enable the mic. A SPST NC or SPDT switch is required. The NKK G3B15AH-R-YA was selected, it is a SMT right angle switch. Its a bit larger than I'd like but there are not as many options for SPDT right angle SMT switches.

## Schematic
![schematic](img/schematic.jpg)

I start by recreating the schematic for the Shure mute switch in [KiCad](https://www.kicad.org). There is one additional resistor to add. The microphone runs at 1.5V - 3.6V. We need to drop the 5V DC bias from the body back to that range.

![bodypack input](img/bodypack.jpg)


|           |           |             |            |         |               |    |         |   |
|-----------|-----------|-------------|------------|---------|---------------|----|---------|---|
| Vin       | 5 V       |             |            |         |               |    |         |   |
| Vmin      | 1.5 V     |             | R1         | 10000 Ω |               |    |         |   |
| Vmax      | 3.6 V     |             | R2         | 8000 Ω  |               | R3 | 20000 Ω |   |
| Idd@1.8 V | 0.000145A |             |            |         |               |    |         |   |
|           |           |             |            |         |               |    |         |   |
|           |           | Voltage Div |            |         | Current Limit |    |         |   |
|           | RbodyPack | Vout        | Idiv       |         | Vout          |    |         |   |
| ATXD      | 0         | 2.222 V     | 0.00027778 |         | 2.1 V         |    |         |   |
| ULXD      | 1000 Ω    | 2.105 V     | 0.00026316 |         | 1.955 V       |    |         |   |
| QLXD      | 1000 Ω    | 2.105 V     | 0.00026316 |         | 1.955 V       |    |         |   |
| UHFR      | 500 Ω     | 2.162 V     | 0.00027027 |         | 2.0275 V      |    |         |   |

Shure sells a few different lines of wireless systems. Each bodypack supplies the bias voltage slightly differently. Lets check if we can find a combination of resistors that works with all models. In Excel I try out regulating voltage for the microphone by using a voltage divider (R1, R2) and a current limiting resistor (R3).  A 20kΩ current limiting resistor ends up fitting in perfectly. It is already in the mute portion of the circuit and brings drops the voltage into the 1.5V - 3.6V range for the capsule.


## Component Footprints
KiCad libraries include footprints for thousands of devices, but they don't include everything. Using the Footprint Editor, we create new layouts for the microphone and switch.

![mems mechanical](img/mems_mech.jpg)
The microphone datasheet includes mechanical specifications, a pinout, and solder stencil pattern. Using these measurements and some basic math, we add pads that correspond to contacts on the microphone. The footprint editor lets us add pads to different layers of the footprint.

For the microphone, we define the footprint by these layers:
* Copper - This is where we want copper.
* Mask - This is where we want copper to be exposed.
* Paste - Where we want solder paste to be applied. This will be used to create a stencil.
* Silkscreen - This layer adds labels, polarity indicators, and other info to the PCB.
* Drill - Where holes will be drilled. These are often used to connect between layers of the PCB. For the microphone, it is used to create an acoustic port.

![component library](img/footprint.jpg)