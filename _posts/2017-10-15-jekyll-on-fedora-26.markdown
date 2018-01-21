---
layout: post
title: "Installing jekyll on Fedora 26"
date: 2017-10-15 12:05
categories: blog
tags: Fedora jekyll blog ruby
---

Installing the static site generator jekyll on Fedora should be a simple matter, but as usual, there's always a few things that are needed.

Install ruby, ruby-devel, rubygems
    $ sudo dnf install ruby ruby-devel

    Building native extensions.  This could take a while...
    ERROR:  Error installing jekyll
            ERROR: Failed to build gem native extension.
    <...>
    /usr/share/ruby/mkmf.rb:457: in 'try_do': The compiler failed to generate an executable file. (RuntimeError)
    You have to install development tools first.

Let's get the development tools:

    $ sudo dnf install redhat-rpm-config

    $ gem install jekyll
<...>
    19 gems installed

All seems to be installed, but...

    $ jekyll --version
    ".../core_ext/kernel_require.rb:55: in 'require': cannot load such file -- json (LoadError)

We need an extra gem! Why it didn't install it by itself is a mystery:

    $ gem install json

    $ jekyll --version
    jekyll 3.5.2

