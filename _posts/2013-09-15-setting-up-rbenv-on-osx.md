---
layout: post
title: "Setting Up Rbenv on OSX"
description: ""
category: 
tags: []
---
{% include JB/setup %}

I switched from RVM to Rbenv. I am on OSX 10.6.8 and the latest RVM has been a pain to work with. It took
quite a while to setup Rbenv but it was worth it because I haven't ran into any major errors.

Fire up your terminal and let's get started! I am assuming you have Homebrew installed.

    $ brew update
    $ brew install rbenv
    $ brew install rbenv-gem-rehash
    $ brew install ruby-build

Once you have installed rbenv  run this command to setup the paths for rbenv

    rbenv init

If your installation does not recognize the rbenv paths, open .profile and add the following lines:

    $ PATH="$HOME/.rbenv/bin:$PATH"
    $ export PATH

Add this to your .profile

    eval "$(rbenv init -)"

After restarting the terminal or sourcing .profile, install some rubies then rehash rbenv

    $ rbenv install 2.0.0-p247
    $ rbenv install 1.9.3-p448
    $ rbenv rehash

By default rbenv uses the system ruby version, to use the installed version, type in

    $ rbenv global 2.0.0-p247
    $ ruby -v

If one of your project needs a specific version, cd to the app directory then type in (once you cd out of the
directory, rbenv will switch to the global ruby). this will create a .ruby-version file on your app

    rbenv local 1.9.3-p448
    ruby -v

If you want rvm-like gemset functionality, install rbenv-gemset

    $ brew install rbenv-gemset
    $ rbenv gemset create 2.0.0-p247 mygemset
    $ rbenv gemset create 1.9.3-p448 myothergemset
    $ rbenv gemset active

If you want a rails app to use a gemset, cd into an app directory and type in:

    echo -e "mygemset" > .rbenv-gemsets

Make sure that the .ruby-version file specified contains the gemset you specified in .rbenv-gemsets

    rbenv gemset list # will display ruby version and the gemsets under it

Enjoy working with Rbenv! Cheers! \m/