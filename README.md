# producer-mic
![picture of producer-mic](img/hero.jpg)

This mic was created to meet a unique need for a push-to-talk mic for Shure wireless systems. In a live environment with high standards and attention to detail, they did not want the Executive Producer stand out with a comm headset and pack. They opted to go with a more covert IFB based solution.

For many years, a custom Countryman B3 filled this need. It was wired into two way radio surveillance kit. While simple, this design was fragile, difficult to repair, and caused pops when the mic was turned on and off. So I decided to make a microphone that was difficult to repair but at least did not pop when it turned on and off.

This tiny mic attaches to a sleeve using a magnet. Users are intelligible in loud production environments. The project was an overall success!

Below you will find a project design and build log. This repo contains design assets, schematics, board, and footprint libraries.


## Circuit Design
![shure mute switch diagram](img/mute_20switch.gif)

Shure sells inline mute switches, these are often used by NFL Refs to talk to America. A schematic is posted in their knowledge base. The additional caps and resistors in this circuit help clean up small voltage spikes when the switch changes state.

The B3 capsule in the previous iteration of the mic is expensive, especially when it is just used for comm! It was originally chosen for its size, but we can go much smaller!

### Microphone
![mic on a lego stud](img/lego.jpg)
Yes, that is a microphone on a lego stud! This is a Knowles SPH6611LR5H-1 MEMS microphone. This $0.92 component is constructed using processes similar to how microprocessors are made. The diaphragm and amplifier are etched out of silicon. This particular component is bottom ported, the audio passes through the bottom of the PCB into the microphone.

![side view of microphone component](img/mems.jpg)

### Switch
For a Push-To-Talk, the switch needs to be normally closed. Pressing the button opens the switch to un-mute. A SPST NC or SPDT switch is required. The NKK G3B15AH-R-YA was selected, it is a SMT right angle switch. Its a bit larger than I'd like but there are not as many options for SPDT right angle SMT switches.

## Schematic
![producer-mic schematic](img/schematic.jpg)

I start by recreating the schematic for the Shure mute switch in [KiCad](https://www.kicad.org). There is one additional resistor to add. The microphone runs at 1.5V - 3.6V. We need to reduce the 5V DC bias from the bodypack into to that range.

![bodypack input bias circuits](img/bodypack.jpg)


|           |           |             |            |         |               |
|-----------|-----------|-------------|------------|---------|---------------|
| Vin       | 5 V       |             | R1         | 10000 Ω |               |
| Vmin      | 1.5 V     |             | R2         | 8000 Ω  |               |
| Vmax      | 3.6 V     |             | R3         | 20000 Ω |               |
| Idd@1.8 V | 0.000145A |             |            |         |               |
|           |           |             |            |         |               |
|           |           | Voltage Div |            |         | Current Limit |
|           | RbodyPack | Vout        | Idiv       |         | Vout          |
| ATXD      | 0         | 2.222 V     | 0.00027778 |         | 2.1 V         |
| ULXD      | 1000 Ω    | 2.105 V     | 0.00026316 |         | 1.955 V       |
| QLXD      | 1000 Ω    | 2.105 V     | 0.00026316 |         | 1.955 V       |
| UHFR      | 500 Ω     | 2.162 V     | 0.00027027 |         | 2.0275 V      |

Shure sells a few different lines of wireless systems. Each system has a slightly different bias circuit. Lets check if we can find a combination of resistors that works with all models. In Excel, I prototype regulating the voltage using a voltage divider (R1, R2) and a current limiting resistor (R3).  A 20kΩ current limiting resistor ends up fitting in perfectly. Its already used in the mute circuit and drops the voltage within the range required by the microphone.


## Component Footprints
KiCad includes footprints for many devices, but not all. Using the Footprint Editor, we create new layouts for the microphone and switch.

![microphone mechanical specifications](img/mems_mech.jpg)
The microphone datasheet includes mechanical specifications, a pinout, and solder stencil pattern. Using these measurements and some basic math, we add pads that correspond to contacts on the microphone. The footprint editor lets us add pads to different layers of the footprint.

For the microphone, we define the footprint by these layers:
* Copper - This is where we want copper on the PCB.
* Mask - This is where we want copper to be exposed.
* Paste - Where we want solder paste to be applied. This will be later be used to create a stencil.
* Silkscreen - This layer adds labels, polarity indicators, and other info to the PCB.
* Drill - Where holes will be drilled. These are often used to connect between layers of the PCB. For the microphone, its also used to create an acoustic port.

![creating a footprint in KiCad](img/footprint.jpg)

Additional custom footprints are created for the artwork on the front of the mic and the PTT button.

## Design
![design pattern](img/design_inspo.jpg)
I was looking at different triangle based patterns to put on the front of the mic. Could not get them to fit right. A few weeks later I found the perfect art deco pattern while on an architectural tour. This was found at the Town of Lake Water Tower and Municipal Building in Milwaukee Wisconsin.

## PCB Layout
![pcb layout](img/pcb_layout.jpg)
Each symbol in the PCB layout has a corresponding footprint. These footprints are added to the PCB. I know where I want the mic, switch and pads for the cable to go so those are placed first. From there, I place the rest of the components.

After everything is placed, the nets are turned into routes. I use the Route tool draw a path between pads of different components. Ground is done last, for that I use a pour to fill in the rest of the space with ground.

## Fabrication
![A breakdown of different layers used to create PCB](img/pcb_layers.jpg)
The KiCad file goes off to [OshPark](https://oshpark.com) to be fabricated. OshPark makes a panel that includes the producer-mic pcb along with pcbs from dozens of other customers. They make it very easy and affordable to run small prototypes, this board cost $1.70!

The KiCad file also gets uploaded to [OshStencil](https://www.oshstencils.com/). They'll etch a stainless steel solder paste stencil using the paste layer we defined earlier.


| ![Stencil](img/stencil.jpg) | ![Paste Applied](img/paste.jpg) |
|-----------------------------|---------------------------------|
| ![Hotplate](img/hotplate.jpg) | ![Batch of Finished PCBs](img/batch.jpg) |

Time to solder it up! The stencil is placed over the PCB, and the paste is applied by spreading the paste across. The components are applied to the board using a set of precision tweezers. Placement does not need to be perfect, as the flux evaporates, the surface tension of the solder will kind of pull the components into place. It is very cool to see! The boards go onto the hotplate. As the paste heats up, it goes from a grayish color to a metallic silver. When everything is silver, the board is done and can be removed to cool down.


## Case Design and Construction
![laying out the case design in Adobe Illustrator](img/outlines.R1.svg)

The board outline was brought into Adobe Illustrator to work out the case geometry. I also created a model in AutoDesk Fusion360. My friend Dan, a skilled cabinetmaker, was able to cut a few prototypes out of scrap plastic material using a cnc router. Once we had the fit right, it was cut out of wood.

![prototype enclosures](img/case_prototype.jpg)

The hole for the switch and wire are manually drilled and a magnet is glued to the inside of the case. The cable is fished in and soldered to the board. The board is glued in place. The mic is finished!

The mic worked well during its initial testing, but there was a bit of wind noise. I tried out a lav sized deadcat. There was a bit of improvement, but it didn't make up for the weird texture. I ended up going with a small piece of open cell foam that was locally sourced from the lid of my Pelican case. That seemed to resolve the issue.


## BOM
| **Description**        | **Model**      | **Manufacturer** | **Quantity** | **Price Each** | **Total Price** |
|------------------------|----------------|------------------|--------------|----------------|-----------------|
| 0.12uF 805 capacitor   | 08053C124JAT2A | KYOCERA AVX      | 1            | $0.24          | $0.24           |
| 150pF 805 capacitor    | 08051A151J4T4A | KYOCERA AVX      | 1            | $0.16          | $0.16           |
| MEMS microphone        | SPH6611LR5H-1  | Knowles          | 1            | $0.92          | $0.92           |
| 20k 805 Resistor       | ERJ-6ENF2002V  | Panasonic        | 2            | $0.03          | $0.06           |
| 100k 805 Resistor      | ERJ-6ENF1003V  | Panasonic        | 1            | $0.03          | $0.03           |
| SPDT Switch            | G3B15AH-R-YA   | NKK              | 1            | $6.60          | $6.60           |
| PCB                    |                | OSHPark          | 1            | $1.70          | $1.70           |
| TA4F Connector         | RT4FC-B        | Neutrik          | 1            | $2.80          | $2.80           |
| Balanced Lav Mic Cable | W2901          | Mogami           | 6            | $0.67          | $4.02           |
|                        |                |                  |              |                |                 |
|                        |                |                  |              | **Total:**     | $16.53          |