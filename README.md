jtag_vpi
========

TCP/IP controlled VPI JTAG Interface.

    +------------------+     +-----------------+     +------------------+      +----------+
    +                  +     +                 +     +                  +      +          +
    + Testbench client + <=> + JTAG VPI server + <-> + JTAG VPI verilog + <--> + JTAG TAP +
    +                  +     +                 +     +                  +      +          +
    +------------------+     +-----------------+     +------------------+      +----------+
        test_client.c             jtag_vpi.c               jtag_vpi.v             any tap...
    -------------------- TCP  ------------------  VPI ---------------------   --------------
    --------------------      ---------------------------------------------   --------------

A testbench is provided and can be run with:

    cd sim/run
    make sim

The Makefile supports Icarus verilog and Cadence xcelium.
The VCD waveforms can be dumped by specifyin +dump_enable=1 on the command line.
