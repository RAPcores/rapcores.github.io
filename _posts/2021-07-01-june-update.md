---
layout: post
title: "RAPcores June 2021 Update"
excerpt_separator: <!--more-->
author: SJ Kelly
---

Welcome to the June monthly update on RAPcores.
Development in the month of June has been focused on continued support for SoC deployments
and eFPGA systems.

<!--more-->

If you are just hearing about RAPcores, welcome! We are developing Robotic Application Processing cores,
with the goal of enhanced robotic proprioception through motor control. We are leveraging hardware and software
co-design to create tightly coupled motor control and sensing solutions for robotics and advanced manufacturing.

## Register Mapping Complete, Fixed Point Next

In June I finished the preliminary work on register mapping. This yielded a net 20% reduction in
resources and has given interfaces far more access and control to internal registers inside RAPcores.
For context only about 50% of internal registers were covered in the original design. Now we
can access all of them for fewer resources. A big win.
There are still some final integrations to do for fixed point. I suspect we are on target to net
40% resource savings on the system, with a far more maintainable and simple design. For context the
register mapping work deleted over one hundred lines of source from our state machine, and greatly
streamlined adding new capabilities. Once the fixed point work is merged in we can begin exploring
different control modes and more telemetry.


## Quicklogic and Symbiflow

The last week of June was entirely dedicated to bringing up the Quicklogic EOS S3 chip and Symbiflow. The EOS
S3 is a cool little chip with a Cortex M4F and eFPGA. Internally there is a system bus between the ARM core and
eFPGA, allowing us to evaluate how these types of systems will work together in physical hardware. Unfortunately
the EOS S3 eFPGA fabric is somewhat under powered for multi axis control, and the Symbiflow tool chain does not seem as polished as
yosys+nextpnr. I
have started an early port for the EOS S3 in the branch. So far, the port has been relatively easy. In part,
our efforts to improve the parametrics of RAPcores has allowed us to scale down certain precision's to fit on these smaller architectures. 

Once our sensing story is more complete, I have a strong feeling
RAPcores in IoT applications could be a far more important market than even robotics. One of our goals is to improve
the overall efficiency of motor control with better control methods. Our parametric core designs we should be
able to support a wide range of applications in motor control.

QuickLogic is the first publicly traded FPGA vendor to commit to a fully open source stack. There seems to be
many interesting chips in their pipeline, so they will be one to follow. 

## FOSSi Dial-up

I presented our work at the FOSSi Foundation Dial-up on June 15th. The video can be seen [here](https://www.youtube.com/watch?v=zVlGynNwZDo).

## New Hardware

The past few months we have been designing some hardware examples to the evaluate costs and form factors. 
One of these was a minimal and cost effective four axis control board, designed to be paired with a RasPi.
Unfortunately we designed the board around the Ice40 HX8K, which is somewhat difficult to obtain these days
and priced out by the ECP5 25k part. Moreover, the availability of FPGAs is low right now with the semiconductor
shortages. The coming months will likely be a holding pattern on hardware design
 until we get news on availability and future products.



## Conclusion

June was mostly about some big projects wrapping up, and planning for new ones. July will be
full of more experiments and open source developments, so stay tuned!


