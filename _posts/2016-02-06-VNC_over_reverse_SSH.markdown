---
layout: post
title:  "VNC over reverse SSH"
date:   2016-02-06 22:05
categories: blog
tags: ssh vnc
---
Playing with network tunnels is fun, and almost magical. SSH is such a toolbox of neat surprises that keeps on giving the more you learn it.

I've used SSH tunneling in the past mostly to securely access a remote host over VNC. The typical scenario is connecting from work to my home computer and access its desktop, where I use my billing software. The tunnel opens a port on the work computer that redirects all activity on the remote host as it's local.  

    work$ ssh -L 5901:localhost:5900 homeuser@homepc

Then at the work computer, the port 5901 becomes my homepc port 5900, effectively its VNC desktop. Simply opening a VNC viewer at work on localhost:5901 displays the homepc desktop.

But what about the other way around? It's not possible to access the work computer from home because most companies set up firewalls that impede such access.

Enter Reverse SSH. It creates a connection _from_ the host to a remote host but acts as if the connection was the other way around. So this way, the connection is initiated behind the firewall to the outside world, and it's left open to the remote host designated port.

    work$ ssh -R 7000:localhost:22 homeuser@homepc

This would create a reverse SSH tunnel, where the homepc port 7000 acts as work port 22. Now on the homepc, I can log in onto work:

    homepc$ ssh workuser@localhost -p 7000

So far this is beautiful. But surely I should be able to access my work desktop over VNC?

Turns out, the reverse port is transparent in the other direction, so port forwarding works as usual.

    homepc$ ssh -L 5901:localhost:5900 workuser@localhost -p 7000

This would open port 5901 on my homepc, tunneled over the reverse SSH on port 7000 back to the workpc. Of course, the workuser password is the remote work PC in this case, even though the host is localhost. Then, simply opening the VNC viewer on port localhost:5901 would display my work PC desktop in all its glory.



