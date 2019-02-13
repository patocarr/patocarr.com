---
layout: post
title:  "VoIP with OBI202 and CallWithUs"
date:   2017-02-17 23:30
categories: blog
tags: voip
---
Recently decided to drop my Vonage VoIP service and just get a VoIP phone. I can't justify about $35/mo for something I barely use. I've been using CallWithUs successfully for several years, connecting over their local number to call overseas. Also been using them with the Android app "CSipSimple" with limited experience but great results.

Researching VoIP phones, I came across OBiTalk and the extensive features convinced me to try it. The OBi202 has WAN/LAN ports, 2 phone jacks and a USB port to expand storage. Software features include support for Google Voice and many useful VOIP configuration options.

<iframe style="width:120px;height:240px;" marginwidth="0" marginheight="0" scrolling="no" frameborder="0" src="//ws-na.amazon-adsystem.com/widgets/q?ServiceVersion=20070822&OneJS=1&Operation=GetAdHtml&MarketPlace=US&source=ac&ref=tf_til&ad_type=product_link&tracking_id=patocarr-20&marketplace=amazon&region=US&placement=B007D930YO&asins=B007D930YO&linkId=06ca94cc6c47bd8e54c8359123637e4f&show_border=false&link_opens_in_new_window=true&price_color=333333&title_color=0066c0&bg_color=ffffff">
</iframe>

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

