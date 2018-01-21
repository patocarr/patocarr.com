---
layout: post
title: "Trip through hell and back"
date: 2018-01-20 01:48
categories: blog
tags: Fedora upgrade hell
---

  After installing MCUxpresso by manually installing the .deb installer (on Fedora 26, RPM based), things started to misbehave, in a rapidly and devastatingly way. The first symptom was Linux getting stuck at boot time after restarting from using Windows. In an effort to fix things, a broken upgrade just threw things even more sideways.

  The culprits were twofold, but solely initiated by this author.

  The command used to un-package the .deb file, [dpkg-deb](http://man7.org/linux/man-pages/man1/dpkg-deb.1.html) with the option --extract, as it says, it extracts the contents to a target directory. What I didn't know, is that it would *overwrite* the destination folder by deleting and re-creating it.

  In this case, as the target directory was '/' (ie. filesystem root), it deletes the /lib entry which is actually a soft link to /usr/lib. This was the first culprit. Many important system pieces reside under /lib, such as kernel modules and firmware blobs.

  The grub2 entries listed 3 old kernel images that differed from the kernel modules' version under /usr/lib/modules. At the grub2 prompt, entering 'e' the boot options can be manually modified. By changing the linuxefi & initrdefi paths to the running kernel version, the system booted up. The change is temporary until the next restart, but it allowed me to diagnose the system from inside. There, found out that Linux didn't have kernel modules loaded to mount VFAT & NTFS partitions, nor the wifi and webcam modules.

  Using the [grub2-mkconfig](https://fedoraproject.org/wiki/GRUB_2) command, the grub2 list entries were fixed to match the newer kernels.

    $ sudo grub2-mkconfig -o /boot/efi/EFI/fedora/grub.cfg

  The second party to blame is Windows 10. It has a feature that makes the system boot faster, by keeping boot information in the disk partition. All good on the Windows side, except in this state, the disk looks "dirty" to Linux, like a hibernated partition that mounts in read-only mode.

  So after working on Windows and a power cycle, Linux starts and after a few boot messages, it drops to an emergency console. Except nothing works there: there's a cursor and I can type, but any command returns an error. Ctrl-Alt-Del is the only way out, and a few times had to hold the power key down to exit the system cold. Booting into the system with the live flash drive and editing the /etc/fstab, to comment out both partitions helped get the system to boot up.

    The disk contains an unclean file system (0, 0).
    Metadata kept in Windows cache, refused to mount.
    Falling back to read-only mount because the NTFS partition is in an unsafe state. Please resume and shutdown Windows fully (no hibernation or fast restarting.)"
    Error mount /dev/sdb1 at /run/media/user/DATA
    modprobe: FATAL: Module fuse not found in directory /lib/modules/4.14.8-200.fc26.x86_64
    ntfs-3g-mount: fuse device is missing, try 'modprobe fuse' as root.

  The NTFS partition was "dirty" and refused to mount. Strangely the EFI partition, which is VFAT also refused to mount. "Well, two dirty Windows-related partitions...". I managed to disable the Fast Boot option in Windows' Power Management settings, and after powering off, the partitions should have been clean, the error message above went away. Except another message popped up while trying to access the partitions:

    Error mount /dev/sdb1 at /run/media/user/DATA
    modprobe: FATAL: Module fuse not found in directory /lib/modules/4.14.8-200.fc26.x86_64
    ntfs-3g-mount: fuse device is missing, try 'modprobe fuse' as root.

  Perhaps the long wait to upgrade to Fedora 27 would help fix this for good...

  Upgrading was easy, albeit a bit long at about 2 hours.

    $ sudo dnf install dnf-plugin-system-upgrade
    $ sudo dnf --refresh upgrade
    $ sudo dnf system-upgrade download --releasever=27
    $ sudo dnf system-upgrade reboot

  It all went smooth, but the damage was still lurking... At this point, after about 5 hours of frustration, it looked like it all fit together.

  Then I noticed that the Wi-Fi driver wasn't loaded, and while I was using a network cable, this wireless network interface's MAC address is used for a node locked license to run some specialized software. Could Fedora 27 have screwed up the Wi-Fi driver?? Trying it from a live flash drive proved that there was still a problem in the system, even after upgrading.

  The Wi-Fi for the Intel 7265D is managed by the iwlwifi kernel module, which loads a firmware blob at boot time. The driver is failing to load the handful of blob versions that are present in the filesystem under /usr/lib/firmware.

  Things reached a desperate point after about 10 hours, where the only viable option to avoid wasting more productive hours seemed to be reinstalling the system. I started the installer and while selecting partitions to format, I just couldn't fold and accept failure. So I bailed out of the installer and went back to work.

  Trying a few things, the 'dnf update' command attempts to install a pending package called filesystem-fc27 and mysteriously fails, reporting that /lib exists and can't be overwritten. In a sane Fedora 27 install such as the live version, the /lib is a soft link to /usr/lib. Comparing /lib with /usr/lib, it's quite obvious that these sets of files should be together, to the point that's quite a mystery how the system performs like it does without other more obvious issues. So I basically merged the files under /lib to be moved to /usr/lib. Then the empty /lib directory was removed and recreated as a soft link to /usr/lib.

  After restarting, the Wi-Fi driver and the webcam both work. A long and frustrating fight indeed, but victorious! 

  In a nutshell, this is what happened:

 * The dpkg-deb command with the --extract option deleted the /lib soft link to /usr/lib, overwriting it with the files from the package. This is the smoking gun.
 * Upon restart, Linux failed to find its kernel modules in /lib, going into emergency mode.
 * After booting an old kernel by hacking grub2 entries, the system got up.
 * Reinstalling kernel, kernel-modules & issuing grub2-mkconfig, and commenting out the Windows partitions fixed the grub2 kernel entries.
 * Now the system booted up, but Windows partitions wouldnt't mount and were read-only due to Windows Fast Boot option.
 * After fixing the partitions from Windows, they still wouldn't mount due to missing kernel modules, vfat & ntfs.
 * Hoping for a fix to an already frustrating session, upgrade to Fedora 27.
 * The upgrade went okay, but the missing /lib soft link meant some critical kernel files were in /lib and expected to be under /usr/lib. Wi-Fi kernel module failing to load its binary blob was a symptom of this split.
 * Merge /lib onto /usr/lib and re-create the /lib soft link to /usr/lib.
 * Back to normal.

