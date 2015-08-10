---
title: 'Deploy Rails 4.2 App with Capistrano 3 in Webfaction (bonus: Figaro)'
author: Ygam Retuta
layout: post
permalink: /deploy-rails-4-2-with-capistrano-3-webfaction-figaro/
dsq_thread_id:
  - 3403366718
categories:
  - Ruby on Rails
tags: [capistrano, ruby-on-rails]
---
> **VERSIONS: Capistrano 3.3.5, Rails 4.2.0**

Assuming you have a simple Rails app, I will show you how to deploy it via Capistrano 3. I am using Webfaction as my host right now so some things may vary with your setup.

Before we begin, follow the instructions here: <http://docs.webfaction.com/user-guide/access.html#using-ssh-keys>

This is my Gemfile:

    source 'https://rubygems.org'
    
    gem 'rails', '4.2.0'
    gem 'sqlite3'
    gem 'sass-rails', '~> 5.0'
    gem 'uglifier', '>= 1.3.0'
    gem 'coffee-rails', '~> 4.1.0'
    gem 'jquery-rails'
    gem 'turbolinks'
    gem 'jbuilder', '~> 2.0'
    gem 'sdoc', '~> 0.4.0', group: :doc
    
    gem 'figaro'
    gem 'pg'
    gem 'high_voltage', '~> 2.2.1'
    gem "slim-rails"
    
    group :development do
      gem 'capistrano-rails'
    end
    
    group :development, :test do
      gem 'byebug'
      gem 'web-console', '~> 2.0'
      gem 'spring'
    end
    
`cd` into your project directory and then execute`bundle exec cap<br /> install`

uncomment these three lines in `Capfile`

      {% highlight ruby %}
      require 'capistrano/bundler'
      require 'capistrano/rails/assets'
      require 'capistrano/rails/migrations'
      {% endhighlight %}

replace `deploy.rb` contents with (`username` is your Webfaction username, `githubusername` is your Github or whatever scm you prefer, username):
    
        lock '3.3.5'    
        set :application, 'yourappname'
        set :repo_url, 'git@github.com:githubusername/app.git'
        set :deploy_to, '/home/username/webapps/srzph'
        set :pty, true
        
        set :linked_files, fetch(:linked_files, []).push('config/database.yml', 'config/application.yml', 'config/secrets.yml')
        
        set :linked_dirs, fetch(:linked_dirs, []).push('bin', 'log', 'tmp/pids', 'tmp/cache', 'tmp/sockets', 'vendor/bundle', 'public/system')
        
        set :tmp_dir, '/home/app/tmp'
        set :default_env, {'PATH' => "#{deploy_to}/bin:$PATH",'GEM_HOME' => "#{deploy_to}/gems"}
        
        namespace :deploy do
          task :restart do
            on roles(:app) do
              capture("#{deploy_to}/bin/restart")
            end
          end
        end
        
        after 'deploy:finishing', 'deploy:restart'
        

replace `config/deploy/production.rb` contents with (host= your web server, can be viewed in Webfaction dashboard, i.e., `299.webfaction.com`):
    
        role :app, %w{username@host}
        role :web, %w{username@host}
        role :db,  %w{username@host}
        

SSH into your Webfaction hosting, and in a text editor, open `/home/username/webapps/railsapp/nginx/conf/nginx.conf` and replace: `/home/username/webapps/railsapp/hello_world/public` with `/home/username/webapps/railsapp/current/public`. Also set `rails_env` to `production`

In your local workstation, `cd` into your Rails app (if you&#8217;re not already in it) and execute:
    
        bundle exec figaro install
        rake secret
        
    
Then, in your `config/application.yml`, add: `SECRET_KEY_BASE: <whatever rake secret generated>`

ssh to your Webfaction hosting and create an SSH key:

{% highlight ruby %}
ssh-keygen -t<br />
rsa -C "username@host"
{% endhighlight %}
    
then add that to your SCM keys (if using Github, add it in `https://github.com/settings/ssh`

install bundler on your server:
    
        cd $HOME/webapps/app
        export PATH=$PWD/bin:$PATH
        export GEM_HOME=$PWD/gems
        export RUBYLIB=$PWD/lib
        gem install bundler
        

upload `config/database.yml`, `config/secrets.yml`, and `config/application.yml` to `/home/username/webapps/app/shared` (do so via SCP, FTP, or whatever) from your local workstation to the server

in your local workstation, `cd` to your project directory (if you&#8217;re not already in it) and execute:
    
    `cap production deploy`

Sit back and enjoy. If something goes wrong, don&#8217;t hesitate to Google. I am not the absolute authority on this. It works with my setup so there might be something you missed 😛