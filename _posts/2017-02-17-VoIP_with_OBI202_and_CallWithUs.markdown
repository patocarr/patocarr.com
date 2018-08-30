---
layout: post
title:  "VoIP with OBI202 and CallWithUs"
date:   2017-02-17 23:30
categories: blog
tags: voip
---
Recently decided to drop my Vonage VoIP service and just get a VoIP phone. I can't justify about $35/mo for something I barely use. I've been using CallWithUs successfully for several years, connecting over their local number to call overseas. Also been using them with the Android app "CSipSimple" with limited experience but great results.

Researching VoIP phones, I came across OBiTalk and the extensive features convinced me to try it. The OBi202 has WAN/LAN ports, 2 phone jacks and a USB port to expand storage. Software features include support for Google Voice and many useful VOIP configuration options.

<a target="_blank"  href="https://www.amazon.com/gp/product/B007D930YO/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=B007D930YO&linkCode=as2&tag=patocarr-20&linkId=00599e7c8d765d81eb8b463d1a8c6993"><img border="0" src="//ws-na.amazon-adsystem.com/widgets/q?_encoding=UTF8&MarketPlace=US&ASIN=B007D930YO&ServiceVersion=20070822&ID=AsinImage&WS=1&Format=_SL250_&tag=patocarr-20" ></a><img src="//ir-na.amazon-adsystem.com/e/ir?t=patocarr-20&l=am2&o=1&a=B007D930YO" width="1" height="1" border="0" alt="" style="border:none !important; margin:0px !important;" />

Time to install it.

I was reluctant to set up an account with them, as being the geek I am, thought I could get away with it. I did... and didn't.

The set up was simple. Hook up the Internet port to the <a href="https://amzn.to/2PfzeSm">router</a>, plug the phone jack and power it up. Calling \*\*\* on the phone, a voice message reads the IP assigned to it by the router (ie. DHCP). Neat detail.

Opened browser, entered IP... and a 404 error. Turns out, to talk to the configuration screen, the PC has to be on the LAN ethernet jack, not from another computer on the router.

Dialing \*\*9 222-222-222, a call test service plays a message and then echoes back whatever is said. Useful to test NAT issues, etc.

Then entered all CallWithUs settings, checked it had registered on the SIP network, and all seemed good. Try calling my cell, and it rang. Picked up, but I could only hear on the cell whatever was said on the phone, and not the other way around. Opened udp ports 5060, 5080 & 10000 on the router's firewall thinking it was some kind of NAT/filter issue, but didn't help. Also disabled DiscoverPublicAddress, thinking it was the router's fault for not having the SIP ALG feature. Nope, nada. Two hours into this, it was getting frustrating.

I found my way around looking at other generic devices configurations on CallWithUs site, about STUN. I had enabled STUN but didn't specify the server URL, because I didn't know it. So entered it and all is good, voice is heard both ways.

So the final configuration options were:

Username: 506\*\*\*\*\*\* (as provided by CallWithUs)<br>
Password: qZnx\*\*\*\* (idem)<br>
Auth ID: same as Username<br>

Proxy Server: sip.callwithus.com<br>
Proxy Server Port: 5060<br>
STUN Address: stun.callwithus.com<br>
STUN Port: 3478<br>

In the end it's not required to register on OBiTalk website, unless you plan on using their services. It may make the set up a bit faster perhaps, but didn't help me and the STUN gotcha.

