---
layout: post
title: "Tweaks to Fedora 28"
date: 2018-05-31 14:25
categories: blog
tags: Fedora tweaks
---

Upgrading to Fedora 28 was quite a smooth experience, on all 3 of my computers, two upgraded from Fedora 27 and one installed from scratch. Here's a few tweaks I did afterwards to make it better suited for my use case.

##### Nautilus

As you may know, Nautilus is the default "Files" application in Fedora or rather, in Gnome 3.x. There is a Python interface to add extensions and include useful custom functionality. One of these extensions is [nautilus-git-gui](https://github.com/lamsh/nautilus-git-gui) that provides access to Git-Gui and Gitk from the context menu in Nautilus.
To install the extension, we first need to install the python2-nautilus package using dnf, as follows:

    $ sudo dnf install python2-nautilus

Then we clone the above Git repo and copy (or link) it so Nautilus can find it.

    $ git clone https://github.com/lamsh/nautilus-git-gui
    $ ln -s nautilus-git-gui ~/.local/share/nautilus-python/extensions

And we kill the running instance of nautilus, so it restarts and takes the extension:

    $ nautilus -q

After this, right-clicking on a free area in the Nautilus windows, brings up the context menu with two new items: "Git GUI Here" and "Gitk Here".

Another quick addition and really useful, is adding the "Open Terminal Here" in the same context menu. The package is "gnome-terminal-nautilus":

    $ sudo dnf install gnome-terminal-nautilus

and restarting nautilus as above to take effect immediately.

A last tweak to nautilus, is to open Preferences and check the "Sort folders before files", so it groups all folders before any files are listed.

![Screenshot showing the nautilus context menu](/images/2018-05-31-Tweaks-to-Fedora-28/nautilus-context-menu.png)

##### Gnome
### Dash-to-panel
This extension to gnome-shell is growing on me since I installed it a week ago. From its github page: "*An icon taskbar for the Gnome Shell. This extension moves the dash into the gnome main panel so that the application launchers and system tray are combined into a single panel, similar to that found in KDE Plasma and Windows 7+. A separate dock is no longer needed for easy access to running and favorited applications.*"

Github's site of [dash-to-panel](https://github.com/home-sweet-gnome/dash-to-panel)

To install, we'll clone it somewhere and then link (or copy) it from where gnome-shell expects it:

    $ git clone https://github.com/home-sweet-gnome/dash-to-panel
    $ ln -s dash-to-panel ~/.local/share/gnome-shell/extensions/

It's certainly worth trying. The workspace looks cleaner with all icons, menus, etc. grouped at the bottom of the screen. There's too many configuration options to describe. A really nice addition to the Gnome desktop.

### Gnome-tweaks
This application is a must-have and should be installed by default along Gnome 3. You can use it to *tweak* several options regarding the desktop behavior, appearance, extensions, fonts, etc. To install:

    $ sudo dnf install gnome-tweaks


##### Java
Fedora comes with Java installed but without the GUI packages, and trying to run a program that uses them would throw a "Java headless exception". To install the full Java package:

    $ sudo dnf install java-1.8.0-openjdk

