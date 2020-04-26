---
layout: post
title: Using Docker with Petalinux
date: 2018-10-01 22:36
categories: blog
tags: Fedora Petalinux Docker CentOS
---

How to run Petalinux on a non-supported Linux distro without a VM.
Docker is similar to a virtual machine in many respects, but much lighter
and faster, and having a reproducible environment to run Petalinux may
be of useful when switching versions.

### Overview

Docker can be used, among other things, to create a cross-compile environment
to build hardware on a platform where the Petalinux tools may not be compatible
or supported by Xilinx. In my case, Fedora 28 has some issues with Petalinux
that made me look for a supported alternative.

Docker is built from a base file called Dockerfile, that has instructions
what OS to build and post-configuration steps such as dependency packages,
user permissions, etc.

This build that I'm working with is based on CentOS 7.4, the latest version
supported by Petalinux. Starting with the stock CentOS, there's a few
packages that were added, like VNC server (tigervnc-server) and Xfce
desktop for Vivado tools.

### Volumes

Being a pseudo-virtual machine means storage is used by Docker, and these
software packages are very big, making the resulting image potentially huge.
A strategy I'm using is to use bind volumes to separate the OS from the
tools installation and the workspace. In other words, the container has
access to a few designated directories on the host from inside the container.

In this case, I designated three directories to use as bind volumes.

* Tools volume: where Petalinux and Vivado SDK will be installed 
* Workspace volume: from where to run all projects
* Installers volume: where to get the installer binaries

These volumes will be accessible from the container and from the host, and
can be changed by using the ```docker run``` command.

### Building

Docker is built from the Dockerfile, downloading the Linux distro and all
dependencies and updates. The result is an image that is ready to work
on installing the tools without issues.

    $ sudo docker build . -t petalinux-centos

A couple of tweaks were needed to have VNC access and start the Xfce
desktop on boot; copying created ```passwd``` and ```xstartup``` files to
the container.

xstartup:

    #!/bin/sh
    unset SESSION_MANAGER
    unset DBUS_SESSION_BUS_ADDRESS
    exec /usr/bin/xfce4-session
    exec /etc/X11/xinit/xinitrc

The ```passwd``` file can be created by using the ```vncpasswd``` command:

    $ vncpasswd passwd

### Running

The docker run command creates a fresh instance of the image, called a
container.
This container "boots" in under a second and it's ready to go. A simple
```exit``` command stops the container.

    $ sudo docker run --rm --entrypoint=/bin/bash \
          --user=plnx --name=petalinux-centos7 -ti \
          -v /home/user/Downloads:/home/plnx/resources:Z \
          -v /opt/docker-volumes/petalinux-2018.2-centos7.4:/opt:Z \
          -v /home/user/Workspace:/home/plnx/workspace:Z \
          petalinux-centos

I added a VNC server to have a GUI, necessary to run Vivado and SDK from 
the host (or any host on the network potentially). To start it:

    [plnx@e94aab57a34e]$ vncserver -geometry 1500x1000 :1

Notice above, the prompt inside the container changes to the containers' Id.

### Installing tools

From the host we can open a VNC viewer such as Remmina or TigerVNC to see 
the container's GUI at address ```172.17.0.2:1```

Inside the container we can run the Petalinux installer, as usual.

    [plnx@e94aab57a34e]$ ./resources/petalinux-v2018.2-final-installer.run /opt/petalinux

Then, similarly with Vivado installer

    [plnx@e94aab57a34e]$ ./resources/Xilinx_Vivado_SDK_Web_2018.2_0614_1954_Lin64.bin

All files will be installed in the Tools volume under ```/opt```, but in reality
the files reside (in this example) in the host at
```/opt/docker-volumes/petalinux-2018.2-centos7.4/```

### Conclusion

After the tools are installed, running a container is very fast and efficient
and always starts at the same exact point based on the image. The volumes
are persistent as they are located in the host's drive, which can be
helpful when switching tools versions or testing differences between builds.
For my own usage, running a supported OS on my unsupported distro of choice
was the main driver.

Dockerfile
----------

    FROM docker.io/centos:7.4.1708
    MAINTAINER patocarr
    LABEL Version 0.1
    
    ENV container docker
    
    # NOTE: Systemd needs /sys/fs/cgroup directoriy to be mounted from host in
    # read-only mode. (Required).
    # VOLUME [ "/sys/fs/cgroup" ]
    
    # Systemd needs /run directory to be a mountpoint, otherwise it will try
    # to mount tmpfs here (and will fail).  (Required).
    VOLUME [ "/run" ]
    
    RUN yum -y update                      && \
        yum -y install openssh xterm rsync && \
        yum clean all
    
    RUN yum -y install xinetd gcc git zlib-devel ncurses-devel openssl-devel \
          libselinux1 bc unzip minicom screen net-tools gnupg diffstat \
          xorg-x11-server-Xvfb chrpath socat autoconf file \
          libtool bzip2 patch gcc-c++ texinfo automake glib2-devel \
          dos2unix iproute gawk gnutls-devel net-tools tftp-server flex bison \
          libstdc++.i686 glibc.i686 libgcc.i686 libgomp.i686 ncurses-libs.i686 \
          zlib.i686 fontconfig.i686 libXext.i686 libXrender.i686 glib2.i686 \
          libpng12.i686 libSM.i686 usbutils expect wget sudo which ; \
          yum clean all
    
    RUN yum install -y epel-release tigervnc-server firefox
    RUN yum --enablerepo=epel -y groups install Xfce
    RUN yum clean all
    RUN wget http://mirror.centos.org/centos/7/os/x86_64/Packages/libpng12-1.2.50-10.el7.x86_64.rpm
    RUN rpm -ivh libpng12-1.2.50-10.el7.x86_64.rpm
    
    RUN sed -ie 's/disable.*= yes/disable = no/' /etc/xinetd.d/tftp
    
    RUN echo "%sudo ALL=(ALL:ALL) ALL" >> /etc/sudoers
    RUN echo "%sudo ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers
    
    # Set locale
    #RUN localectl set-locale en_US.UTF-8
    ENV LANG en_US.UTF-8  
    ENV LANGUAGE en_US:en  
    ENV LC_ALL en_US.UTF-8 
    
    # Set up a user
    RUN useradd --uid 1000 --groups wheel --system --create-home plnx
    RUN echo "plnx:plnx" | chpasswd
    WORKDIR /home/plnx
    
    RUN chmod +w /opt && chown -R plnx:plnx /opt
    
    USER plnx
    RUN mkdir -p /home/plnx/workspace
    RUN mkdir -p /home/plnx/.vnc
    
    ADD xstartup /home/plnx/.vnc/xstartup
    ADD passwd /home/plnx/.vnc/passwd
    
    USER root
    RUN chmod 600 /home/plnx/.vnc/passwd
    RUN chown plnx /home/plnx/.vnc/passwd
    RUN rm /tmp/.X1-lock || :
    
    USER plnx
    CMD /usr/bin/vncserver :1 -geometry 1280x800 -depth 24 && tail -f /home/plnx/.vnc/*:1.log
    
    
    EXPOSE 22
    EXPOSE 5901
    
    
    
