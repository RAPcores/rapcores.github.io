# Verilog Quality Assurance

Software Quality Assurance has become pretty easy in recent years. Cheap or free
continous integration (CI) services mean incremental changes can be automatically
tested. 

The HDL community is experiencing a rennaisance, supported by many great free
and open source tools for simulation, synthesis, place and route, and bit stream generation.
With the typical proprietary tools they are heavily GUI based, and may be difficult
to run on a server. Moreso without paying a license or registering. With
FOSS HDL tools we can run them wherever and whenever, giving HDL projects
a new way to do QA and CI.

When we started developing RAPcores there was no clear guide on how to setup and
run automated QA on a Verilog or HDL project. We hope that this post can help
provide a guide for new starting projects.

## Getting the dependencies

Rapcores primarily uses two main tools for our verilog-to-fpga flow:

- Yosys
- NextPNR

The first issue we encountered was getting dependencies. For the initial work
the team was all using self-compiled or downloaded versions of the tools.
Self-compiled tools are nice for testing at the bleeding edge or obtaining some
native architecture optimizations to reduce performance, but they pose and issue
for rapid testing. First, the test suite in RAPcores only takes a few minutes
to run. Second, building the tools from scratch for each development or test environment
is a waste of time.

Fortunately many Linux distributions or language package managers ship Yosys and Nextpnr.
Many though are out of date, which poses another challenge. To address this we
opted to use the Nix Package manager which has relatively recent tools and
provides good stability.

Nix provides use stable and consistent tooling that can relatively easily be setup
on developer computers and CI servers. You can take a look at our shell.nix file here.
This includes all the basic tooling required for developing the RAPcores project.
We also use the Cachix GitHub action to setup our CI environment for running our
systhesis and bitstream tests.

Note: Sometime after our decision to use Nix packages of the tooling, the YosysHQ team
developed YoWASM distributions avaialble in Pip and a FPGA-builtools pipeline that
may be also good solutions.

## Supporting Multiple Front-ends

During the OpenMPW-one ASIC project we had some major last minute issues with our
simulation. The base Caravel project used iverilog whereas we had mostly tested
under yosys sim. This yielded many parser errors and late nights frantically squashing
the issues that were preventing our simulation from running. The purpose of QA and
continual improvement is to make sure these issues never happen again. To assure of this
we parse RAPcores source files through the following Verilog front-ends:

- Yosys
- IVerilog
- Verilator
- SVLint

This has turned out to be one of the most useful sanity checks in our project. Yosys
is typically very forgiving of malformed statements, whereas IVerilog and Verilator more
strictly adhear to the IEEE standards. This is very handly for identifying disconnected
wires or bit width mismatches. 

## Setting up a CI

As mentioned previously, Nix and Github Actions proved to be a simple solution
for getting our tooling setup and automated. The Workflow files can be seen here.


## Running some tests



## The Mess We Are In

The title of a [great talk](https://www.youtube.com/watch?v=lKXe3HUG2l4) by the late Joe Armstrong. Also
the scenario we put up in RAPcores. One aspect of RAPcores is to provide a foundation
for all different types of motor and motion controllers. This means we have many
options for things such as motor count, bit counts, and protocol widths. Part of this
is to allow for slidng scales between resource use and prcision, but also to address different
markets that use different motor types and counts. We had one problem, now we have two.

Solid QA is useful in this scenario because it make sure that hardware that is not
on a developer's desk or testbench will still be supported and tested. Conversely,
having many configurable options allows for the discovery of bugs and bad abstractions
in our source.

The board configuration system is inspired by those used in Embedded MCU land. Each board has
the following:

- Pin Definition
- Makefile options
- Verilog options

With these three files a new board can be supported without modifying the RAPcores
source. This also proves to be a powerful abstraction when benchmarking, as one
can see how a given feature effects resource use and speed on the given device.
Moreover, we can also compare given configurations across architectures.

## Future Work

Despite the rumors, good QA is supposed to speed up development not slow it down.
A good deal of time was spent setting up the CI and discovering what to test.
Now that most of this infrastructure is setup we can move faster and avoid
repeating past mistakes.