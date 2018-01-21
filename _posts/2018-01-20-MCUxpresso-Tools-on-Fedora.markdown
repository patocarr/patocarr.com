---
layout: post
title: "Installing MCUxpresso Config Tools on Fedora"
date: 2018-01-20 18:25
categories: blog
tags: Fedora NXP MCUxpresso install
---

MCUxpresso Config Tools package comes with its installer for Ubuntu (14,16) in .deb.bin format, a self-extracting command-line installer. In order to install it in Fedora (or CentOS or RHEL), we'll extract it and then copy its files to the filesystem.

    $ cd ~/Download (or download location)

This command extracts the ```.deb``` to the ```mcux-tools``` directory:

    $ dpkg-deb -x MCUXPRESSO-CT-LIN64-DEB-PACKAGE.deb mcux-tools/

Then we copy (or move) the extracted files under the ```/opt``` directory:

    $ sudo cp -r mcux-tools/opt/nxp /opt

 The path to the application in the ```com.nxp.mcuxpresso-config-tools-v4.desktop``` file may need to be fixed to point to the actual install location, in italics below:

    [Desktop Entry]
    Type=Application
    Version=4.0
    Name=MCUXpresso Config Tools v4.0
    Comment=MCUXpresso Config Tools v4.0
    Icon=/opt/nxp/MCUX_CFG_v4/bin/icon.xpm
    Exec=env UBUNTU_MENUPROXY=0 /opt/nxp/MCUX_CFG_v4/bin/tools
    Path=/opt/nxp/MCUX_CFG_v4/bin
    Categories=Development;

The last step would be to copy the ```.desktop``` file, so it shows in the Applications menu.

    $ cp mcux-tools/share/applications/com.nxp.mcuxpresso-config-tools-v4.desktop ~/.local/share/applications

or system-wide for all users to:

    $ sudo cp mcux-tools/share/applications/com.nxp.mcuxpresso-config-tools-v4.desktop /usr/share/applications



