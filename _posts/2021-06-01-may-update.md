---
layout: post
title: "RAPcores May 2021 Update"
excerpt_separator: <!--more-->
author: SJ Kelly
---

Welcome to the first monthly update on RAPcores.
Development in the month of May has been focused on foundations for the RAPcores project,
with many new developments for future project sustainability.

<!--more-->

If you are just hearing about RAPcores, welcome! We are developing Robotic Application Processing cores,
with the goal of enhanced robotic proprioception through motor control. We are leveraging hardware and software
co-design to create tightly coupled motor control and sensing solutions for robotics and advanced manufacturing.

## Release

In May we released 0.2 of RAPcores. This is more a pedantic and symbolic gesture. 0.1 and 0.2 are API compatible,
with the in-development 0.3 version representing the intention to make breaking changes to the protocol and semantics.
This is more for our internal development because we do not have devices in production,
but if a random person is riding our development churn, you're welcome. 

[0.2 Release Notes](http://rapcores.org/rapcores/releases.html#pathfinder2)

## LibRAPcores C Bindings

We have moved the "librapcores" project into the rapcores tree to allow for better coordination of
development. librapcores are the C Bindings to interface with RAPcores (currently over SPI). We
also are developing a "rapcore-cli" tool to allow telemetry streams and diagnostics. This tool now lives
in the main project repository as well.


## Verilog Parametrics

RAPcores is one of our first large-scale HDL projects,
so we are still getting up to speed on managing complexity of HDL projects.
A key aspect of managing complexity in Verilog is the use of parametrics.
These may be thought of as templates in C++ or the like. Given some static
options (parameters) the source code may be transformed before interpretation
to generate specfic code for a given module or function. It is common in traditional
programming languages to use templates to elide unnessecary logic or vary data type
sizes. In Verilog parameters are very much used the same to change port widths or logic flows.

Recently we added parametric cosine table generators in [#203](https://github.com/RAPcores/rapcores/pull/203).
This greatly simplifies our build steps, and on FPGA it allows us to pre-populate BRAM data used for
fast cosine lookups. This is a good example of a static computation done in pure verilog using parameters.
We are continuing towards this organization style as it is far more powerful than Macros, and does not involve
external build tools, allowing for better portability.

### FPGA Hardware Abstraction Layer 

Speaking of portability, I started a proof-of-concept for an [FPGA HAL](https://github.com/RAPcores/fpga-hal). The purpose of the
demonstrations was to port the Ice40 PLL calculations to Verilog such that they are evaluated
based on parameters using constant functions. This project started as a challenge to
understand the limits of parametric Verilog, and I was uncertain if diophantine equations could be solved while 
retaining sythensizable verilog. However the results are promising so far, and this
approach could lead to improved portability of Verilog between FPGAs. We currently
only use a few PLLs, and only a small component of board configuration is done in Make,
(rather than Verilog macro defines). The FPGA-HAL is low priority for us at the moment,
but may be important in the future as we need to consider use of Hard-IP cores in certain areas,
or consider different FPGAs for production deployments.

## System Verilog

Since Verilog and System Verilog are under the same IEEE1800 standard, the future of interesting features
is in System Verilog, with Verilog remaining the subset of Systems Verilog.
We have started by enabling Systems Verilog support
in Yosys and IVerilog, which is a [few line change](https://github.com/RAPcores/rapcores/pull/204/files) to the build scripts. This has already improved warnings
and error reporting, as System Verilog disabiguates some aspects of Verilog more cleanly.

## Fixed Point

Inside RAPcores we have developed a movement interpolater we call the "DDA" or "digital diference amplifier".
This has been developed with the team at Synthetos as a translation of the software-approach from their G2 CNC/Robotics firmware
into a hardware approach in RAPcores. With the DDA we can precisely time and coordinate steps among multiple axes, allowing for linear
velocity changes given constant acceleration. Currently we treat the DDA as a "step" generator, with the internal
interface re-integrating these step pulses into encoder locations.

For a few months now we have been thinking about achieving the best resource use possible on the Lattice Ice40 and ECP5 architectures.
We have also been thinking about delta-sigma modulators for ADC applications. We realized we were nearly at an approach
that would fuse a fixed-point concept allowing for fractional steps (such as is required when interfacing with encoders),
a variable frequency adder, and a simple phase-tracking apparatus for motor commutation. In jargon, a fixed point delta-sigma style
adder.

We have a simple fixed-point conversion in [#207](https://github.com/RAPcores/rapcores/pull/207), that provides this unification.
The best part is the LUT usage is reduced on a feature-rich (4 axes and 4 encoders, Ice40 LP8K) board setup from 94% to 73%.
This is accomplished by aggressive carry-chain use and simplified pipelines by using the sign of the increment arguments. 
More space, more features.


## Register Mapping and Wishbone SoC Bus

Register Mapping is key to implementing RAPcores as part of a SoC or soft-core system. Our current protocol
and state machine is quite fragile, and tightly coupled together. Moreover, we are unable to
operate the RAPcore as part of a system-on-chip. Decoupling the state machine from the protocol transaction
layer should greatly improve flexibility, scalability, and driver interfaces.

We therefore started some research into developing the state machine and register map into a Wishbone-compatible bus,
with our SPI interface applied as a module ontop of these components.
By using Wishbone bus we should enforce a much cleaner register mapping and simplify the transaction layers
in the state machine. Our hope is this brings similar resource reduction such as the fixed-point project,
and also allow enhance capabilities on platforms such as the [Caravel SoC platform](https://efabless.com/).

We suspect that the framed-protocol as implemented in SPI is unlikely to change greatly. However, there will
be some improvements to fully utilize the register map over SPI. This includes simplified read/write transactions
and more complete access to internal registers as read-only.


## Motor Control

The month of May has been heavily focused on the infrastructure around motor control. Cool stuff
with motors has been on the back burner while we figure out how to move data around chips effectively.
We have made some big updates and clarifications to our [motor control
guide](http://rapcores.org/rapcores/motor_control.html), and added some insight for three phase BLDC control.
We nearly have a working three phase BLDC commutator,
and final validation is waiting on test hardware. Our open-loop control story has been strengthening,
and once some major infrastructure issues are sorted our we will begin focused development on closed loop.

As part of [#203](https://github.com/RAPcores/rapcores/pull/203) we were able to increase our base microstep count from 64x to 256x,
or any arbitrary power of two. The improvement of 256x from 64x on a 200 step/rev stepper motor is minimal,
but will likely be more noticable on motors with fewer steps/electrical cycles such as BLDC.

## Conclusion

That wraps up our first monthly update. Stay tuned for the June update where we start to bring these improvements together.


## Upcoming Events

In June we will be presenting a high level overview and our ASIC shuttle experience on the [FOSSI-Dialup event on youtube](https://www.fossi-foundation.org/).

We will be at [MRRF 2021](https://www.eventbrite.com/e/2021-midwest-reprap-festival-mrrf2021-tickets-148696331731) in Goshen, Indiana.






