# Cam Feeder for PandaPlacer

Custom 3D-printable SMT tape feeder system for the PandaPlacer / PandaPlacer CC1 pick-and-place machine.

> Status: prototype / work in progress
> This project is still being tested and refined.

---

## Why I Designed This Feeder

When I received my PandaPlacer, I first tried to build and use the original feeder design. The original feeder uses a foot that engages with the holes of the SMT tape, pulls the tape forward, and then retracts by sliding over the tape.

In my build, I had difficulty making this work reliably. The mechanism depends a lot on the correct friction during the retracting movement. The cover tape pulling mechanism also relies on friction between 3D printed parts and an O-ring.

Because 3D printed parts are not very precise and their friction can vary a lot depending on material, printer settings, wear, and surface finish, I found the feeder difficult to tune. I wanted a feeder that did not depend so much on friction between plastic parts.

However, I liked some design choices of the original feeder, especially the way the feeder can be removed from the rail support and locked in place. This project tries to keep those good ideas while changing the tape advancing mechanism.

---

## Design Concept

One idea was to use an encoder wheel, similar to the LumenPnP feeder. However, the encoder and photointerrupter take space in the feeder thickness, and the motor must stop immediately when the signal is received. Also, the wheel engages only one or a few holes in the SMT tape.

Instead, this feeder uses a **cam mechanism**.

A rotating cam moves a follower that engages with the holes of the SMT tape. The cam defines the motion mechanically, so the exact motor rotation is not critical.

The motion has four main phases:

1. **Engage**
   The follower moves up and the spikes enter the tape holes.
   In the current design, 9 spikes engage with the tape holes, making the contact more reliable even with 3D printed parts.

2. **Advance**
   The follower moves horizontally and pulls the SMT tape forward by 4 mm.

3. **Disengage**
   The follower moves down and releases the tape.

4. **Stop zone**
   The follower reaches a dead zone and triggers a limit switch.
   The exact motor stop position is not very critical because the follower is already disengaged.

---

## Feeder Electronics

Each feeder has a simple analog control circuit and does not require a microcontroller.

A capacitor is connected to the gate of a MOSFET. The feeder controller charges this capacitor to start the feeder. When the cam reaches the final position, a limit switch discharges the capacitor and stops the motor.

The same signal line is used both to start the feeder and to sense whether the feeder has completed the movement.

Each feeder has 3 pins:

```text
SIGNAL
+12 V
GND
```

The cover tape is pulled by a second motor, controlled by a second limit switch.

---

## Feeder Sizes

The current mechanical widths are:

| Tape width | Feeder width |
| ---------- | -----------: |
| 8 mm       |        12 mm |
| 12 mm      |        16 mm |
| 16 mm      |        20 mm |

Feeders can be placed on the rail in 4.5 mm steps, with about 1 mm gap between adjacent feeders. This makes it possible to mix 8 mm, 12 mm, and 16 mm feeders in any order without leaving unused rail space.

---

## Feeder Controller Board

Each feeder controller board controls up to 8 feeders.

Main functions:

* Uses a shift register to command 8 feeders
* Provides 12 V feeder signals
* Detects if any feeder has not yet completed its motion
* Allows mixed feeder widths on the same rail

Each output is split into 3 sets of vertical pin headers spaced by 4.5 mm. This allows each feeder to connect to the controller even when feeders are placed in different rail positions.

---

## Main Controller Board

The main controller board uses an Arduino Nano.

Main functions:

* Controls the feeder controller shift registers
* Sends a pulse to start the selected feeder
* Waits until the previous feeder motion is completed before sending the next pulse
* Reads the feeder busy / completed signal
* Controls a Padauk microcontroller programming socket

The programming socket is intended for a workflow where the main controller operates a servo to pull the socket, sends a signal to the Padauk programmer, waits for the microcontroller to be programmed, and then the pick-and-place machine picks the programmed microcontroller and places it on the PCB.

---

## Repository Contents

Planned repository structure:

```text
mechanical/
  STL files
  STEP files
  source CAD files

electronics/
  feeder PCB
  feeder controller board
  main controller board
  schematics
  PCB files
  BOM

firmware/
  Arduino Nano main controller code

docs/
  photos
  assembly notes
  wiring notes
  testing notes
```

---

## Notes

This project is experimental. The design may still change after testing.

Before building or using it, please check:

* Motor direction
* Limit switch timing
* Tape advance accuracy
* Cover tape pulling reliability
* 12 V and 5 V wiring
* Controller signals
* Mechanical fit on the PandaPlacer rail

---

## Disclaimer

This is an independent DIY feeder project and is not an official PandaPlacer product.

Use it at your own risk. Test carefully before connecting it to a machine or using it with valuable components.
