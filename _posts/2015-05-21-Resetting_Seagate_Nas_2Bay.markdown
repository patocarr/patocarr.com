---
layout: post
title:  "Resetting Seagate NAS 2-Bay to factory"
date:   2015-05-21 23:22
categories: blog
tags: seagate nas reset
---
The concern of losing digital data is always present. With one hard drive failure all your memories may be gone forever. So I keep several copies of my oversized media collection such as photos, videos and movies.

Over the years I've been adding external hard drives to the collection of devices where to store this data, but it still is not a final solution in terms of reliability, backup frequency and lacking useful features like sharing or accessing remotely.

I've tried playing with the <a href="https://amzn.to/2wySHqn">Raspberry Pi</a> with an attached <a href="https://amzn.to/2N36GxV">1TB 3.5" external hard drive</a> with good results, using OwnCloud but found it unusably slow.

So got a <a href="https://amzn.to/2NwKFEy">Seagate NAS 2-Bay</a> unit.

<a target="_blank"  href="https://www.amazon.com/gp/product/B00LM6LOG0/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=B00LM6LOG0&linkCode=as2&tag=patocarr-20&linkId=7c2986285a5cdde981f44358c3028554"><img border="0" src="//ws-na.amazon-adsystem.com/widgets/q?_encoding=UTF8&MarketPlace=US&ASIN=B00LM6LOG0&ServiceVersion=20070822&ID=AsinImage&WS=1&Format=_SL250_&tag=patocarr-20" ></a><img src="//ir-na.amazon-adsystem.com/e/ir?t=patocarr-20&l=am2&o=1&a=B00LM6LOG0" width="1" height="1" border="0" alt="" style="border:none !important; margin:0px !important;" />

All looks good until ... the setup. The first time you log in, it *should* ask to set the initial user and password for the administrator, but in my case, it's just happily asking to *enter* my credentials. Except that I never had a chance to set them!

What I believe happened was the NAS was bought by someone before me and after s/he set it up, the unit was returned to eBay. When I ordered it, they must have sent me that unit already set up, without doing a factory reset. After all, eBay would have no idea how to do it, as it's just a Seagate reseller.

After much hair pulling it was time to try some other way. There's a little reset button at the back of the unit, but it just looks pretty. It's supposed to reset the initial credentials, but it did nothing.

Time to pull the hard drives out and see what happens when the unit is powered on without them. Oh... on my PC, the browser pointed to its address reports that there's no drives connected and doesn't ask for credentials! Hopeful feeling.
While in this screen, I plug in both drives and the screen changes to a 3 option radio button selector.

  * a- Try to recover data in found drives.
  * b- Reset credentials to factory values.
  * c- Erase all drives and reset all data to factory values.

I selected b) but apparently all drive data was removed as well. It also downloaded an update and restarted by itself. All took about 10-15 minutes.

Finally, it asked about the initial username and password!


[Update 8/29/2018]

The <a href="https://amzn.to/2NwKFEy">Seagate NAS 2-Bay</a> is still performing nominally after more than 3 years being online and used every day. Great purchase! I'm getting ready to replace its <a href="https://amzn.to/2MXe3af">hard drives</a> and have had such good luck with Seagate that will continue buying from them.
<iframe src="//rcm-na.amazon-adsystem.com/e/cm?o=1&p=48&l=ur1&category=computers_accesories&banner=038B3BT3V0V7G44XKF82&f=ifr&linkID=d17d343e85121b713e7c9c4525bb5582&t=patocarr-20&tracking_id=patocarr-20" width="728" height="90" scrolling="no" border="0" marginwidth="0" style="border:none;" frameborder="0"></iframe>

