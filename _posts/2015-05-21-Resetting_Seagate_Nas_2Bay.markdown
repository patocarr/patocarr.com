---
layout: post
title:  "Resetting Seagate NAS 2-Bay to factory"
date:   2015-05-21 23:22
categories: blog
tags: seagate nas reset
---
The concern of losing digital data is always present. With one hard drive failure all your memories may be gone forever. So I keep several copies of my oversized collection of media such as photos, videos and movies.

Over the years I've been adding external hard drives to the collection of devices where to store this data, but it still is not a final solution in terms of reliability, backup frequency and lacking useful features like sharing or accessing remotely.

I've tried playing with the Raspberry Pi with an attached 1TB 3.5" external hard drive with good results, using OwnCloud but found it unusably slow.

So got a Seagate NAS 2-Bay unit.

All looks good until ... the setup. The first time you log in, it *should* ask to set the initial user and password for the administrator, but in my case, it's just happily asking to *enter* my credentials. I never had a chance to set them!

What I believe happened was the NAS was bought by someone else and after s/he set it up, the unit was returned to eBay. When I ordered it, they must have sent me that unit already set up, without doing a factory reset. After all, eBay would have no idea how to do it, as it's just a Seagate reseller.

After much hair pulling it was time to try some other way. There's a little reset button at the back of the unit, but it just looks pretty. It's supposed to reset the initial credentials, but it did nothing.

Time to pull the hard drives out and see what happens when the unit is powered on without them. Oh... on my PC, the browser pointed to its address reports that there's no drives connected and doesn't ask for credentials! Hopeful feeling.
While in this screen, I plug in both drives and the screen changes to a 3 option radio button selector.

  * a- Try to recover data in found drives.
  * b- Reset credentials to factory values.
  * c- Erase all drives and reset all data to factory values.

I selected b) but apparently all drive data was removed as well. It also downloaded an update and restarted by itself. All took about 10-15 minutes.

Finally, it asked about the initial username and password!

