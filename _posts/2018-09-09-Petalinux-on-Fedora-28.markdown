---
layout: post
title: "Installing Petalinux 2018.2 on Fedora 28"
date: 2018-09-09 12:08:00
categories: blog
tags: Fedora Petalinux install
---

Fedora 28 is not a supported platform for Petalinux 2018.2, but with a little effort, it can be installed with a workaround. We will install bash-4.3 from source rpm and use it to install Petalinux.

### Dependencies

As described in the [Petalinux installation manual](https://www.xilinx.com/support/documentation/sw_manuals/xilinx2018_2/ug1144-petalinux-tools-reference-guide.pdf) there's a few dependencies that must be installed before attempting to install Petalinux.

    $ sudo dnf install diffstat gcc-c++ chrpath socat autoconf libtool
    $ sudo dnf install zlib-devel ncurses-devel openssl-devel rpcgen

The _ncurses_ library component is too "new" for Petalinux, so we symlink'd hoping the previous version is backwards compatible. This allows the compiler to complete, but there's some graphical issues with the ncurses GUI interfaces.

    $ sudo ln -s /lib64/libncursesw.so.6.1 /lib64/libncursesw.so.5

### Installing bash

Attempting the Petalinux install at this stage will complete, but it would leave the yocto/source directory malformed, apparently due to this error thrown during install "environment: line 277: locked_signs: bad array subscript". This causes the mentioned directory to failed to be populated. This [forum post](https://forums.xilinx.com/t5/Embedded-Linux/Peta-Linux-2018-1-install-failure/td-p/853223) describes the issue in more detail.

The issue is related to bash-4.4 on Fedora 28 and Debian, which is newer than the version expected by the supported platforms, bash-4.3.

So, let's install bash-4.3 on Fedora 28 to work around this limitation. First we get the source package for the last known bash-4.3 version, from [Fedora Buildsystem](https://koji.fedoraproject.org/koji/buildinfo?buildID=805704), specifically from the [bash-4.3 source link](https://kojipkgs.fedoraproject.org//packages/bash/4.3.43/4.fc26/src/bash-4.3.43-4.fc26.src.rpm).

In order to compile bash using this rpm source, we need a few tools, namely rpmbuild, bison and texinfo.

    $ sudo dnf install rpmbuild bison texinfo

We install the rpm source which will create a directory by default under `~/rpmbuild` and populate it.

    $ rpm -i bash-4.3.43-4.fc26.src.rpm

The compiled binaries will end up in `~/rpmbuild/BUILD/bash-4.3/`

So far, so good, bash is installed but the system won't find it automatically, so we'll change the location temporarily just to install Petalinux.

    $ sudo mv /bin/bash /bin/bash-4.4
    $ sudo ln -s ~/rpmbuild/BUILD/bash-4.3/bash /bin/bash
    $ bash --version
    GNU bash, version 4.3.43(1)-release (x86_64-redhat-linux-gnu)

### Installing Petalinux

With bash out of the way, let's install Petalinux 2018.2

    $ ./petalinux-v2018.2-final-installer.run /opt/pkg/petalinux

After the installer completes, all directories under yocto/source are populated correctly!

After the install, we revert the change to bash to the default version, deleting the symlink and reinstating the original bash version.

    $ sudo rm /bin/bash
    $ sudo mv /bin/bash-4.4 /bin/bash


