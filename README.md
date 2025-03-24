# producer-mic
This mic was created to work with Shure ULX-D and Axient Digital wireless systems. Prior to this device, the venue was using a custom Countryman B3 mic wired into a PTT radio surveillance kit. While simple, this design was fragile, difficult to repair, and caused clicks when the mic was turned on and off.

## Component Selection
![shure mute diagram](img/mute_20switch.gif)

Shure ships inline mute switches. They also supply supply a schematic for it in their knowledge base. The additional caps and resistors in this circuit help clean up small voltage spikes when the switch changes state.

The B3 capsule in the previous iteration of the mic is expensive, especially when it is just used for comm! It was originally chosen for its size, but we can go much smaller!

![mic on a lego stud](img/lego.jpg)
Yes, that is a microphone on a lego stud! This is a Knowles SPH6611LR5H-1. This $0.92 component is constructed using processes similar to how other ICs are made. The diaphragm and amplifier are etched out of silicon. This particular component is bottom ported, the audio passes through the bottom of the PCB into the microphone.

![bottom port](img/mems.jpg)

For a Push-To-Talk, the switch needs normally be closed to mute the audio



## Circuit Design
![mems mechanical](img/mems_mech.jpg)