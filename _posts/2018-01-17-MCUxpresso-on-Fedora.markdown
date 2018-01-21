---
layout: post
title: "Installing MCUxpresso IDE on Fedora"
date: 2018-01-17 16:45
categories: blog
tags: Fedora NXP MCUxpresso install
---

MCUxpresso IDE comes with its installer for Ubuntu (14,16) in .deb.bin format, a self-extracting command-line installer. In order to install it in Fedora (or CentOS or RHEL), we'll extract it and then copy its files to the filesystem.

    $ cd ~/Download (or download location)

This command extracts the .bin to the /tmp/mcux directory:

    $ sudo sh mcuxpressoide-10.1.1_606.x86_64.deb.bin --noexec --target /tmp/mcux

    Creating directory /tmp/mcux
    Verifying archive integrity... All good.
    Uncompressing mcuxpressoide installer  100%
    -rwxrwxr-x. 1 root root      1451 Jan  2 04:39 install.sh
    -rw-r--r--. 1 root root  20987428 Jan  2 04:39 JLink_Linux_x86_64.deb
    -rwxrwxr-x. 1 root root 715466378 Jan  2 04:39 mcuxpressoide-10.1.1_606.x86_64.deb
    -rw-rw-r--. 1 root root     25202 Jan  2 04:39 ProductLicense.txt

Since we've extracted using 'sudo', now the files and directory will be owned by root; let's fix that.

    $ cd /tmp/mcux
    $ sudo chown <username> *

As you can see, there's two .deb files extracted. Looking into them, there's no post-processing done to the files other than extracting them to the filesystem from the .deb's.
The J-Link that comes bundled is v6.20g although at time of writing, the latest version is of v6.22f, and also found in .rpm format in Segger's site. [JLink v622](https://www.segger.com/downloads/jlink/JLink_Linux_V622f_x86_64.rpm)
The bundled version installs to /opt/SEGGER and in /usr/bin some soft links to it.

So to extract the J-Link .deb:

    $ sudo dpkg-deb -x JLink_Linux_x86_64.deb ./

_NOTE:_ This .deb expands to a directory opt and *REMOVES* it first! I installed on / and it deleted my /opt tree and recreated it! I lost some tools' installations by it! Here's the log as I did it:

    [user@laptop mcux]$ ls /opt/
    google/       SpiderOakONE/ Xilinx/
    [user@laptop mcux]$ sudo dpkg-deb -x JLink_Linux_x86_64.deb /    <- DON'T DO THIS!
    [sudo] password for user: 
    [user@laptop mcux]$ ls /opt/
    SEGGER

and then the MCUXpresso .deb:

    $ sudo dpkg-deb -x mcuxpressoide-10.1.1_606.x86_64.deb ./

The files extracted will end up in:

    ./usr/lib/udev/rules.d/56-pemicro.rules
    ./usr/lib/udev/rules.d/85-mcuxpresso.rules
    ./usr/share/applications/com.nxp.mcuxpressoide.desktop
    ./usr/local/mcuxpressoide-10.1.1_606/     (most files are here)

The resulting executable IDE will be located at 
```./usr/local/mcuxpressoide-10.1.1_606/ide/mcuxpressoide```

  From the local directory location (ie. ```/tmp/mcux/```) the files may be manually copied to their filesystem locations.

The first time I ran the IDE, it complained about a missing directory, so it's perhaps necessary to create it first.

    $ sudo mkdir -p /usr/share/NXPLPCXpresso


