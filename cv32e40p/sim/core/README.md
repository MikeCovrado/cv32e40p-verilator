Simulation Directory for CV32E40P Core Testbench
================================================
This is the directory in which you should run all tests of the Core Testbench.
The testbench itself is located at `../../tb/core` and the test-programs are at
`../../tests`.  See the README in those directories for more information.

The core testbench and associated test-programs can be run using **_Verilator_**.

RISC-V GCC Compiler "Toolchain"
-------------------------------
Pointers to the recommended toolchain for CV32E40P are in `../TOOLCHAIN`.

Running your own C programs
---------------------
A hello world program is available and you can run it in the CV32E40P Core testbench.
Invoking `make` with no arguments will compile and run it.

The hello world program is located in the `../../tests/programs/custom` folder.
The relevant sections in the Makefile on how to compile and link this program can be found under `Running custom programs`.
Make sure you have a working C compiler (see above) and keep in mind that you are running on a very basic machine.

Running Assembler programs
-----------------------------
If you have a C or assembly program in `../../tests/core/custom/your_test_dir`
then the following will work with Verilator:<br>
```
make veri-test TEST=your_test_dir
```
