---
layout: post
title: "Upgrading from Rails 3 to Rails 4: Multiple Assets Manifest"
description: ""
category: 
tags: []
---
{% include JB/setup %}

I converted a Rails app I made with Rails 3 to Rails 4 last night and ran into one of the shenanigans of conversion.
I am using Capistrano 2.5.15 with Rails 4 and found that the mechanism for assets manifests is changed in Rails 4.
You can look at the gory details here:

https://iprog.com/posting/2013/07/capistrano-errors-when-upgrading-to-rails-4

To save you some headaches, all you have to do for now is log in to your webserver with SSH or FTP and CD to
capistrano's `shared` folder and delete the Rails 3
`manifest.yml`. Let me know if you run into some other roadblocks for conversion.

Cheers!
