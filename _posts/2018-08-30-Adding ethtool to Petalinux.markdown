---
layout: post
title:  "Adding ethtool to Petalinux"
date:   2018-08-30 21:15
categories: blog
tags: Xilinx Petalinux ethtool
---

This post documents the steps required to add ethtool to an existing Petalinux project.

### Sourcing Petalinux tools

Before running any Petalinux command, you need to source their tools.

1. Navigate to the Petalinux project directory

        $ cd /home/user/myproject/qBoot

2. Source the Petalinux tools

        $ source /opt/pkg/petalinux-v2018.2/settings.sh


### Checking the flash page (partition) allocation

1. Navigate to the Petalinux project directory

        $ cd /home/user/myproject/qBoot

2. Run the following command to open misc/config System Configuration

        $ petalinux-config

<img width="800px" src="/images/2018-08-30-Petalinux-ethtool/sshot1.png"/>
<!--    ![System Configuration](/images/2018-08-30-Petalinux-ethtool/sshot1.png) -->
3. Select "Subsystem AUTO Hardware Settings"

<img width="800px" src="/images/2018-08-30-Petalinux-ethtool/sshot2.png"/>
<!--    ![Subsystem AUTO Hardware Settings](/images/2018-08-30-Petalinux-ethtool/sshot2.png)-->

4. Select "Flash Settings"

   As shown, the first (boot) partition starts at address 0x0 and has a size of 0x600000 which makes the second (bootenv) partition start at address 0x600000. The second (bootenv) partition has a size of 0x80000 which makes the third (kernel) partition start at address 0x680000. Therefore, when the BOOT.BIN is programmed into the flash its offset should be 0x0, whereas the offset for the image.bin should be 0x680000.

<!--   ![Flash settings](/images/2018-08-30-Petalinux-ethtool/sshot3.png) -->
<img width="800px" src="/images/2018-08-30-Petalinux-ethtool/sshot3.png"/>

### Customizing and building the kernel

1. Navigate to the Petalinux project directory

        $ cd /home/user/myproject/qBoot

2. Run the following command to open the Configuration GUI

        $ petalinux-config -c rootfs

<!--    ![Configuration GUI](/images/2018-08-30-Petalinux-ethtool/sshot4.png)-->
<img width="800px" src="/images/2018-08-30-Petalinux-ethtool/sshot4.png"/>

3. To enable the ethtool, navigate to: Filesystem Packages -> console -> network -> ethtool > [\*] ethtool3

   Enable the ethtool option

<!--    ![Enabling ethtool](/images/2018-08-30-Petalinux-ethtool/sshot5.png)-->
<img width="800px" src="/images/2018-08-30-Petalinux-ethtool/sshot5.png"/>

4. After configuring all the parameters, Save & Exit

   Note: The phytool can be added to the build according to the following step:

        $ vim project-spec/meta-user/conf/petalinuxbsp.conf

        IMAGE_INSTALL_append += "\
        phytool \
        "

5. Clean up the build by running the following command

        $ petalinux-build -x mrproper

6. Build Petalinux. This will take a while.

        $ petalinux-build

7. When completed successfully, navigate to the following directory to locate the *image.ub* kernel file and zynqmp_fsbl FSBL file:

        $ cd images/linux

8. Load the kernel image (updated or a brand new board) once into the QSPI flash using the SDK or Vivado tool. In case of using the SDK tool: 

- Select Xilinx/Program Flash menu option
- Select image.bin as the Image File
- Set Offset to 0x680000
- Set FSBL file to zynqmp_fsbl.elf.

    Note: the kernel can only be programmed once a BOOT.BIN has already been programmed into the Zynq.

### Building the boot image

The boot image may need to be re-built due to an updated kernel or bitfile.

1. Navigate to the generated linux directory under the Petalinux project directory

        $ cd /home/user/myproject/qBoot/images/linux

2. If you have an updated logic bitfile, delete system.bit and copy the new bitfile into this directory and rename it to system.bit, then generate BOOT.BIN as follows:

        $ petalinux-package --boot --u-boot --fsbl zynqmp_fsbl.elf --fpga system.bit  pmufw pmufw.elf --force

3. Load the boot image into the QSPI flash using the SDK or Vivado tool. In case of using the SDK tool:

- Select Xilinx/Program Flash menu item
- Select BOOT.BIN as the Image File
- Set offset to 0x0
- Set FSBL File to zynqmp_fsbl.elf


