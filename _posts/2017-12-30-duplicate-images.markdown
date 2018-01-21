---
layout: post
title: "Finding duplicate images"
date: 2017-12-30 22:48
categories: blog
tags: images photos mongodb python
---

Image collections such as photos can grow over time to become hard to manage, and double so, when their backup is also involved. Images get mistakenly duplicated and finding duplicates as simple as it sounds, soon turns to be a very complicated affair to solve. This Python tool attempts to help in cleaning duplicates, by storing a hash of the images in question and comparing them among the collection. Even resized images with a different name are discovered.

Installing
----------

Installing consists of getting the script, and its the dependencies for the required Python libraries and the MongoDB database. The script runs only on Python3; on Python2 it errors "ImportError: cannot import name TimeoutExpired" from the library submodule.

    $ sudo apt install mongodb

    $ git clone https://github.com/philipbl/duplicate-images

    $ cd duplicate-images/
    $ pip3 install -r requirements.txt

Then I had to stop the MongoDB service running or the script would stop due to a clash with the running instance.

    $ sudo systemctl stop mongodb

When I ran the script the first time, it threw an error regarding the MongoDB database, so after I ran this command, the error went away. YMMV.

    $ mongod --db=./db
    <Ctrl-C> to cancel

Then running the script as:

    $ python3 duplicate-images.py add ~/Pictures

would hash all the images under that directory and add them to the database. This process by default runs 8 children processes, so it's extremely computing intensive. The option --parallel=<num_processes> limits these parallel processes.

In order to display the differences, this command creates a tree with the actual path to the images, side by side, and all put into a series of HTML pages for perusal, including a "Delete" button on each image. This command opens that page in the default browser:

    $ python3 duplicate-images.py find
  
