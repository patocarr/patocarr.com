---
layout: post
title:  "Installing vncserver with systemd unit on Fedora 28"
date:   2018-09-26 22:01:41
categories: blog fedora linux
---
Installing a VNC server should be a simple task but it comes with a few curveballs.

### Setup

Install TigerVNC server

    $ sudo dnf install tigervnc-server

Log in to VNC manually such that it creates password and .vnc files

    $ vncserver :1

    You will require a password to access your desktops.
    
    Password:
    Verify:
    Would you like to enter a view-only password (y/n)? n
    
    New 'storm:1 (<USER>)' desktop is storm:1
    
    Creating default startup script /home/<USER>/.vnc/xstartup
    Creating default config /home/<USER>/.vnc/config
    Starting applications specified in /home/<USER>/.vnc/xstartup
    Log file is /home/<USER>/.vnc/storm:1.log

Now use a VNC viewer to access desktop, ie. Remmina on display :1

and finally kill the temporary VNC server

    $ vncserver -kill :1
    Killing Xvnc process ID 20915

### Systemd Unit

Copy from systemd unit template

    $ sudo /lib/systemd/system/vncserver@.service /etc/systemd/system/

Edit unit to customize for user. Replace '\<USER\>' by the user logging into VNC

    $ sudo vi /etc/systemd/system/vncserver@.service

I had an issue where the VNC server would die after about a minute of running, apparently due to some SELinux permissions on .vnc directory. The message was:
```
Sep 26 21:40:39 storm systemd[1]: vncserver@:1.service: Failed with result 'timeout'.
Sep 26 21:40:39 storm systemd[1]: Failed to start Remote desktop service (VNC).
-- Subject: Unit vncserver@:1.service has failed
```

These SELinux related commands helped the situation:

    $ chcon -t xserver_exec_t /home/<USER>/.vnc
    $ sudo semanage fcontext -a -t xserver_exec_t "/home/<USER>/.vnc(/.*)?"

Now we can start the systemd unit

    $ sudo systemctl start vncserver@:1.service

And to make it start on each subsequent system boot

    $ sudo systemctl enable vncserver@:1.service

### Reference:

[Fedora System Administrator Guide](https://docs.fedoraproject.org/en-US/Fedora/26/html/System_Administrators_Guide/ch-TigerVNC.html#s1-vnc-server)

