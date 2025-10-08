<!---
   Copyright (c) 2020, 2021, 2022 OpenHW Group
   Copyright (c) 2025 Michael Thompson

   Licensed under the Solderpad Hardware Licence, Version 2.0 (the "License");
   you may not use this file except in compliance with the License.
   You may obtain a copy of the License at

   https://solderpad.org/licenses/

   Unless required by applicable law or agreed to in writing, software
   distributed under the License is distributed on an "AS IS" BASIS,
   WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
   See the License for the specific language governing permissions and
   limitations under the License.

   SPDX-License-Identifier: Apache-2.0 WITH SHL-2.0
--->

# cv32e40p-verilator
Simple testbench for the CV32E40P that runs on Verilator.

This testbench is based on the (now depreciated), "core" testbench developed for the CV32E40P in the [core-v-verif](https://github.com/openhwgroup/core-v-verif) project.
Throughout this document there will be multiple references to `$YWC`.  This is the absolute path to `your working copy` of this repository.
To use this repository you will need:

- A Linux machine (this testbench is was developed on Ubuntu 24.04, and varients have been successfully run under older revisions of Ubuntu as well as Debian and CentOS).
- Python3 and a set of plug-ins. The current plug-in list is kept in `$YWC/bin/requirements.txt`.
- A GCC cross-compiler (aka "the [Toolchain](https://github.com/openhwgroup/core-v-verif/blob/master/mk/TOOLCHAIN.md#core-v-toolchain). Even if you already have a RISC-V toolchain, please do follow that link and read [TOOLCHAIN.md](https://github.com/openhwgroup/core-v-verif/blob/master/mk/TOOLCHAIN.md) for recommended ENV variables to point to it.
- [Verilator](https://veripool.org/guide/latest/install.html), version v5.040 or better.

An easy way to get the Python plug-ins installed on your machine is:
```
   $ git clone https://github.com/MikeCovrado/cv32e40p-verilator.git $YWC
   $ cd $YWC/bin
   $ pip3 install -r requirements.txt
```
**Note:** Virtual python environments can be used if desired.

Once the above is in place type the following:

    $ cd $YWC/sim/core
    $ make

**Note:** Check the required [shell environment variables](https://github.com/openhwgroup/core-v-verif/tree/master/mk#required-corev-environment-variables).

The above will compile the RTL and the testbench using Verilator, compile the 'hello-world' test-program using the Toolchain and run it on the "Verilated" model.
The simulation run should produce the following:

    HELLO WORLD!!!
    This is the OpenHW Group CV32E40P CORE-V processor core.
    CV32E40P is a RISC-V ISA compliant core with the following attributes:
        mvendorid = 0x602
        marchid   = 0x4
        mimpid    = 0x0
        misa      = 0x40001104
        XLEN is 32-bits
        Supported Instructions Extensions: MIC

Where is the RTL?
-----------------

You will not find the RTL here.
The RTL is automatically cloned into $YWC/core-v-cores/<core_name> by the Makefiles, as needed.
If you have successfully run the hello-world test above, then you may have noticed that the RTL was cloned to $YWC/core-v-cores/cv32e40p/rtl (take a look!).
The URL, branch and hash of the cloned RTL is obtained from $YWC/cv32e40p/sim/ExternalRepos.mk (again, take a look!).
You can override these variables on the `make` command line or by setting shell environment variables.

Do I need a Reference Model?
----------------------------

The short answer is **not yet**.
Future work for this repository is to use an Instruction Set Simulator (ISS) as a reference model of the CV32E40P core (the Device Under Test).
The goal is to compare the state of the DUT to the state of the reference model after each instruction is retired.
Without a comparison to a reference model, the pass/fail status of a given simulation is mostly vacuous.

There are several popular options for a RISC-V ISS, including [Spike](https://github.com/riscv-software-src/riscv-isa-sim) [Whisper](https://github.com/chipsalliance/SweRV-ISS) and [Imperas OVPsim](https://www.ovpworld.org/technology_ovpsim).
Both Spike and Whisper are open-source ISS models.
At the time of this update (2025-10-07) we are working on integrating the Spike ISS as the reference model.
A contribution to integrate another reference model into CORE-V-VERIF would be welcome.


Supported Simulators
--------------------

As the name of the repository implies, this testbench was specifically written for Verilator, a free an open source simulator.
There is no reason why this testbench cannot be ported to other simulators, but at this time we do not wish to take on the technical debt of maintaining scriptware in support of other simulators.

CV32E40P Directory Tree (simplified)
------------------------------------

Below $YWC you will find a directory named *cv32e40p*.
This directory contains all of the CV32E40P-specific sources to compile and run simulations on the CV32E40P CORE-V core.
The tree below is a somewhat simplified expansion of the directory highlighting the names, locations and purposes of key directories and files.

```
  cv32e40p
    ├── bsp                                         // Board-support Package
    ├── regress                                     // Regression configurations (coming soon)
    ├── sim
    │   ├── README.md
    │   ├── Common.mk
    │   ├── core                                    // Place to run simulations of the "core" testbench
    │   |   ├── Makefile
    │   |   └── README.md
    │   ├── TOOLCHAIN.md
    │   └── tools
    ├── tb
    │   ├── README.md
    │   └── core                                    // the "core" testbench
    │       ├── dp_ram.sv
    │       ├── mm_ram.sv
    │       ├── tb_top.sv
    │       ├── tb_top_verilator.cpp
    │       └── tb_top_verilator.sv
    ├── tests                                       // test-programs
    │   ├── cfg
    │   │   ├── default.yaml
    │   │   ├── no_pulp.yaml
    │   │   ├── num_mhpmcounter_29.yaml
    │   │   ├── ovpsim_no_pulp.ic
    │   │   └── pulp.yaml
    │   └── programs
    │       ├── corev-dv                            // configurations for randomly generated test-programs
    │       │   ├── corev_rand_arithmetic_base_test
    │       │   │   ├── corev-dv.yaml
    │       │   │   └── test.yaml
    │       |   ├── ...
    │       |   |
    │       │   └── corev_rand_jump_stress_test
    │       │       ├── corev-dv.yaml
    │       │       └── test.yaml
    │       └── custom                              // "custom" (manually written) test-programs
    │           ├── hello-world
    │           │   ├── hello-world.c
    │           │   └── test.yaml
    │           ├── ...
    │           |
    │           └── debug_test
    │               ├── debug_test.c
    │               └── test.yaml
    └── vendor_lib                                  // Libraries from third-parties (in need of a major update!)
        ├── README.md
        ├── google
        ├── imperas
        ├── riscv
        └── verilab
```
