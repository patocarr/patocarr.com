---
layout: post
title: "Vivado simulator fix on Fedora 28"
date: 2018-05-26 12:35
categories: blog
tags: Fedora Xilinx Vivado blog
---

Xilinx Vivado HLx is not currently supported on the latest version of Fedora (28) but since living on the edge entails some suffering, I decided to stick with 28 and plough my way through the challenge. I installed Vivado 2018.1 on Fedora 28, and found some issues during installation, detailed on this post of mine in Xilinx' forums: [Installing Vivado 2018.1 on Fedora 28](https://forums.xilinx.com/t5/Installation-and-Licensing/Installing-Vivado-2018-1-on-Fedora-28/m-p/855996#M21928)

After the installation went through with the above fixes, the RTL simulation still crashed when launched. The error shown was not very revealing...

    ERROR: [XSIM 43-3409] Failed to compile generated C file xsim.dir/system_tb_behav/obj/xsim_3.c.
    ERROR: [XSIM 43-3915] Encountered a fatal error. Cannot continue. Exiting... 
    run_program: Time (s): cpu = 00:00:08 ; elapsed = 00:00:08 . Memory (MB): peak = 7517.402 ; gain = 0.000 ; free physical = 42243 ; free virtual = 79865
    INFO: [USF-XSim-69] 'elaborate' step finished in '8' seconds
    INFO: [USF-XSim-99] Step results log file:'/home/pcarr/Work/tux/vivado-test1/project_2/project_2.sim/sim_1/behav/xsim/elaborate.log'
    ERROR: [USF-XSim-62] 'elaborate' step failed with error(s). Please check the Tcl console output or '/home/pcarr/Work/tux/vivado-test1/project_2/project_2.sim/sim_1/behav/xsim/elaborate.log' file for more information.
    ERROR: [Vivado 12-4473] Detected error while running simulation. Please correct the issue and retry this operation.
    launch_simulation: Time (s): cpu = 00:00:11 ; elapsed = 00:00:10 . Memory (MB): peak = 7517.402 ; gain = 4.008 ; free physical = 42222 ; free virtual = 79844
    ERROR: [Common 17-39] 'launch_simulation' failed due to earlier errors.

The file ``elaborate.log`` doesn't show much information, saying that there was a problem compiling a file, though the compiler seems to be clang and using gcc as linker.

    /opt/Xilinx/Vivado/2018.1/data/../tps/llvm/3.1/lnx64.o/bin/clang  -fPIC -c -std=gnu89 -nobuiltininc -nostdinc++ -w  -Wl,--unresolved-symbols=ignore-in-object-files -fbracket-depth=1048576 -I/opt/Xilinx/Vivado/2018.1/data/../tps/llvm/3.1/lnx64.o/bin/../lib/clang/3.1/include -fPIC -m64  -I"/opt/Xilinx/Vivado/2018.1/data/xsim/include" "xsim.dir/system_tb_behav/obj/xsim_3.c" -O0 -sim -o "xsim.dir/system_tb_behav/obj/xsim_3.lnx64.o" -DXILINX_SIMULATOR
    Linking with command:
    /usr/bin/gcc -Wa,-W  -O -fPIC  -m64  -Wl,--unresolved-symbols=ignore-all  -o "xsim.dir/system_tb_behav/xsimk"   "xsim.dir/system_tb_behav/obj/xsim_0.lnx64.o" "xsim.dir/system_tb_behav/obj/xsim_1.lnx64.o" "xsim.dir/system_tb_behav/obj/xsim_2.lnx64.o" "xsim.dir/system_tb_behav/obj/xsim_3.lnx64.o" "/opt/Xilinx/Vivado/2018.1/lib/lnx64.o/librdi_simulator_kernel.so"  "/opt/Xilinx/Vivado/2018.1/lib/lnx64.o/librdi_simbridge_kernel.so"

Let's try to get more verbosity to find out what's happening, using the Settings/Simulation dialog, or optionally, these Tcl commands:

    set_property -name {xsim.elaborate.mt_level} -value {off} -objects [get_filesets sim_1]
    set_property -name {xsim.elaborate.xelab.more_options} -value {-v 1} -objects [get_filesets sim_1]

![Screenshot to show simulation verbosity (-v 1) and turn off multithreading (-mt off)](/images/2018-05-26-Vivado-simulator-fix-on-Fedora-28/Screenshot from 2018-05-27 01-08-51.png)

Then re-launching the simulation, we get more insight into the problem:

    /opt/Xilinx/Vivado/2018.1/data/../tps/llvm/3.1/lnx64.o/bin/clang  -fPIC -c -std=gnu89 -nobuiltininc -nostdinc++ -w  -Wl,--unresolved-symbols=ignore-in-object-files -fbracket-depth=1048576 -I/opt/Xilinx/Vivado/2018.1/data/../tps/llvm/3.1/lnx64.o/bin/../lib/clang/3.1/include -fPIC -m64  -I"/opt/Xilinx/Vivado/2018.1/data/xsim/include" "xsim.dir/system_tb_behav/obj/xsim_3.c" -O0 -sim -o "xsim.dir/system_tb_behav/obj/xsim_3.lnx64.o" -DXILINX_SIMULATOR
    /opt/Xilinx/Vivado/2018.1/data/../tps/llvm/3.1/lnx64.o/bin/clang: error while loading shared libraries: libncurses.so.5: cannot open shared object file: No such file or directory
    ERROR: [XSIM 43-3409] Failed to compile generated C file xsim.dir/system_tb_behav/obj/xsim_3.c.
    ERROR: [XSIM 43-3915] Encountered a fatal error. Cannot continue. Exiting... 
    run_program: Time (s): cpu = 00:00:09 ; elapsed = 00:00:09 . Memory (MB): peak = 6832.422 ; gain = 0.000 ; free physical = 43364 ; free virtual = 80995
    INFO: [USF-XSim-69] 'elaborate' step finished in '9' seconds
    INFO: [USF-XSim-99] Step results log file:'/home/pcarr/Work/tux/vivado-test1/project_2/project_2.sim/sim_1/behav/xsim/elaborate.log'
    ERROR: [USF-XSim-62] 'elaborate' step failed with error(s). Please check the Tcl console output or '/home/pcarr/Work/tux/vivado-test1/project_2/project_2.sim/sim_1/behav/xsim/elaborate.log' file for more information.
    ERROR: [Vivado 12-4473] Detected error while running simulation. Please correct the issue and retry this operation.
    launch_simulation: Time (s): cpu = 00:00:13 ; elapsed = 00:00:11 . Memory (MB): peak = 6832.422 ; gain = 0.000 ; free physical = 43343 ; free virtual = 80974
    ERROR: [Common 17-39] 'launch_simulation' failed due to earlier errors.

Aha, there's a missing library ``libncurses.so.5`` as seen in the error message above. Let's find out where this library is on the filesystem:

    $ locate libncurses.so.5
    /opt/Xilinx/Vivado/2018.1/lib/lnx64.o/SuSE/libncurses.so.5
    /opt/Xilinx/Vivado/2018.1/lnx64/tools/gdb_v7_2/libncurses.so.5
    /usr/lib64/libncurses.so.6
    /usr/lib64/libncurses.so.6.1
    /usr/lib64/libncursesw.so.6
    /usr/lib64/libncursesw.so.6.1
    /usr/lib64/vlc/plugins/gui/libncurses_plugin.so

With some luck, this soft link would help trick the compiler:

    $ sudo ln -s /usr/lib64/libncurses.so.6 /usr/lib64/libncurses.so.5

And it did! Now Vivado simulator works normally :)

