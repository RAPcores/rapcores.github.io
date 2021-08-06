---
layout: post
title: "RAPcores July 2021 Update"
excerpt_separator: <!--more-->
author: SJ Kelly
---

Welcome to the July monthly update on RAPcores.
Development in the month of July has seen substaintial
improvements to the base system, with resource
efficiency improvements.

<!--more-->

If you are just hearing about RAPcores, welcome! We are developing Robotic Application Processing cores,
with the goal of enhanced robotic proprioception through motor control. We are leveraging hardware and software
co-design to create tightly coupled motor control and sensing solutions for robotics and advanced manufacturing.

## Results of Fixed Point and Reg Map 

Last month, we merged in the initial register mapping work.
This was a nice ~20% resource improvements. I
wrote at the time, that I hoped the addition of
my fixed point work would hit around a 50% net
reduction in resource utilization.

I'm happy to say this has nearly been achieved. On
my four axis TinyFPGA-BX board with four encoders,
we went from 98% to 52% resource utilization,
with around 30% clocking improvements. A large 
part of this is due to carry-chain inference
and several register de-duplications.

By using the fixed point model,
we (theoretically) address modulus issues with encoders,
and allow for highly precise stepping frequencies.
I still need to work on documentation and finalizing
our register mapping. There are still a more finishing
touches before we can begin developing the software APIs.
In particular, we have some seriously abused parametrics
that make the register mapping a headache to interface with.
Mistakes were made, and they are being addressed. I think
once this work is complete, we will have a system
suitable for more interfaces with a relatively common
API amongst all three.


## Documentation Overhaul

Documentation is important in any open source project.
Since day one we have been working to keep documentation
for the project with the repo. This has been important,
especially in a rapidly evolving project such as ours.
For example, our ASIC deployment was a 0.1 release,
which has a substantially different protocol from what is
stage for 0.3. Since the documentation for 0.1 is in that release,
when we get our ASIC 11-10 months later, we can easily go
back to that release + documentation for 0.1, while we
push forward with big changes in 0.3.

This month we doubled down on automating some parts of
the documentation, and improving reproducability.

One tool in particular I am quite excited about is TerosHDL.
If you haven't tried it, I cannot recommend it enough.
It allows for the generation of nice internal documentation
for your verilog modules. [I did a little bit of
work to integrate it with Sphinx.](http://rapcores.org/rapcores/internals.html)
It is not perfect yet,
but with an additional pass and with the help of some
upstream bug fixes, it should look quite nice.

In addition to TerosHDL, [we started sprinkling some
nice Wavedrom graphics](http://rapcores.org/rapcores/register_map.html)
throughout our project to help
document the register maps. This is also a very nice piece
of software, so big thanks to the devs. 

## Standing on Shoulders

It would be fair to say that every month the last year,
some metric of RAPcores has doubled to our benefit.
From multi-channel, bandwidth improvements, error rates,
and resource utilization. This month, I realize there
is little juice left to squeeze out of these big
foundational improvments. Many of these improvements
executed over tha pat 3 months were identified earlier
in the project. We simply did not have time to execute
on them.

Now with the semiconductor shortage, we have really had
no choice but to slow things down and work on the
foundations for the past few months.
However, I think we are nearing the point
many that these low-hanging fruits are nearly exhausted.

The next logical step in the maturity of the project,
is stewardship. Simply put, "What are we doing, that
someone else is doing better". And consequence,
could we adopt and contribute to projects that will
greatly accelerate our development process. In software
this is a little bit easier. You might just call this
"collaboration". In HDL/RTL there are fewer
options, so we need to look hard.
I've identified a few
key areas. One is our common cells. Things such as clock dividers,
edge detectors, and FIFOs. These are fun and educational
to do, but not something we should maintain or vendor.

Pulp Platform's Common Cells is one promising project
that already has many modules that can benefit RAPcores.
I have spent some time this month working on exploring this
as a common library set for the project. This work
has already started, and I hope to share some results soon.


## Conclusion

June was mostly about some big projects wrapping up, and planning for new ones. July will be
full of more experiments and open source developments, so stay tuned!


