---
title: Enable Rails 4.2 Bower with rails-bower in Webfaction
author: Ygam Retuta
layout: post
permalink: /rails-4-2-bower-webfaction/
dsq_thread_id:
  - 3438144310
categories:
  - Ruby on Rails
tags:
  - bower
  - ruby-on-rails
  - webfaction
---
> **Versions: Node 0.10.35, Capistrano 3.3, Ruby on Rails 4.2, bower-rails 0.9.1**

Log into your Webfaction console and install node.js from source. As of writing, here is the current tar file:

<http://nodejs.org/dist/v0.10.35/node-v0.10.35-darwin-x86.tar.gz>

After that, use npm to install bower

    npm install -g bower
    

Following from our previous tutorial:

<http://www.ygamretuta.info/deploy-rails-4-2-with-capistrano-3-webfaction-figaro/>

edit:

    set :default_env, {'PATH' => "#{deploy_to}/bin:$PATH",'GEM_HOME' => "#{deploy_to}/gems"}
    

to include the home bin:

    set :default_env, {'PATH' => "/home/username/bin:#{deploy_to}/bin:$PATH",'GEM_HOME' => "#{deploy_to}/gems"}
    

Edit `config/deploy.rb` to add a task to run `rake bower:install`

    desc 'Install bower'
    namespace :bower do
      task :install do
        on roles(:all) do
          within release_path do
            execute :rake, 'bower:install'
          end
        end
      end
    end
    
    before 'deploy:updated', 'bower:install'
    

Now you have enabled a bower-rails-powered application!

Cheers!