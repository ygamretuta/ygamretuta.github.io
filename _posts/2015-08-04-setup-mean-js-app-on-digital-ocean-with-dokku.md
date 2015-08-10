---
title: Setup Mean.js App on Digital Ocean with Dokku
author: Ygam Retuta
layout: post
permalink: /setup-mean-js-app-on-digital-ocean-with-dokku/
dsq_thread_id:
  - 4000875974
categories:
  - Mean JS
tags:
  - digital-ocean
  - meanjs
---
> app name throughout the tutorial is `meanjs`. This is a droplet created in Digital Ocean. It is now deleted along with hardcoded IPs

Create a Droplet:

[<img src="http://www.ygamretuta.info/wp-content/uploads/2015/08/Screen-Shot-2015-08-04-at-5.38.32-AM.png" alt="Screen Shot 2015-08-04 at 5.38.32 AM" class="alignleft size-medium wp-image-71" />][2]


<div class="clearfix">
</div>

Take note of the IP address of the newly created app

[<img src="http://www.ygamretuta.info/wp-content/uploads/2015/08/Screen-Shot-2015-08-04-at-5.46.47-AM.png" alt="Screen Shot 2015-08-04 at 5.46.47 AM" class="alignnone size-full wp-image-75" />][4]

Login into the droplet and Update local settings:

    sh -c "echo 'LANG=en_US.UTF-8\nLC_ALL=en_US.UTF-8' > /etc/default/locale"
    reboot
    

visit the IP address of the app and configure the settings. Usually you can just leave the defaults. Then click `Finish Setup`

[<img src="http://www.ygamretuta.info/wp-content/uploads/2015/08/Screen-Shot-2015-08-04-at-6.25.28-AM.png" alt="Screen Shot 2015-08-04 at 6.25.28 AM" class="alignnone size-full wp-image-84" />][5]

on your local terminal, clone the meanjs repo and install dependencies

    $ git clone https://github.com/meanjs/mean.git meanjs
    $ cd meanjs
    $ npm install
    

add the Dokku app as a remote to our meanjs app. Take note that `meanjs` is currently our app name, so the format is `git remote add <custom_origin_name> dokku@<droplet_ip>:<custom_app_name>`

    $ git remote add dokku dokku@128.199.208.252:meanjs
    $ git push dokku master
    

log in to the remote server and install the mongodb plugin for Dokku

    $ git clone https://github.com/jeffutter/dokku-mongodb-plugin.git /var/lib/dokku/plugins/mongodb 
    $ dokku plugins-install
    

[<img src="http://www.ygamretuta.info/wp-content/uploads/2015/08/Screen-Shot-2015-08-04-at-7.16.52-AM.png" alt="Screen Shot 2015-08-04 at 7.16.52 AM" class="alignnone size-medium wp-image-97" />][6]

make sure that admin\_pw and pass\_meanjs exists:

    cd /home/dokku/.mongodb
    

**if not, run:**

    $ rm -r /home/dokku/.mongodb
    $ docker stop <mongodb_container_id_from_docker_ps>
    $ docker rmi -f "jeffutter/mongodb:latest"
    $ reboot
    

then start the installation of the plugin again.

Start mongodb and create database:

    $ dokku mongodb:start
    $ dokku mongodb:create meanjs
    

[<img src="http://www.ygamretuta.info/wp-content/uploads/2015/08/Screen-Shot-2015-08-04-at-8.27.23-AM.png" alt="Screen Shot 2015-08-04 at 8.27.23 AM" class="alignnone size-full wp-image-98" />][7]

set initial ENV vars

    $ dokku mongodb:link meanjs
    

**if you happen upon the creation saying a user exists, run:**

    $ dokku mongodb:console
    $ use meanjs-production
    $ db.dropUser('meanjs')
    

Now we have to modify a couple of things. First, look at the port redirection happening in `docker ps -a` for the mongodb image (jeffutter/mongodb:latest, looks like: `0.0.0.0:32769->27017/tcp`), then modify the value of MONGO\_URI and MONGO\_URL

    $ docker ps -a
    $ dokku config:set meanjs MONGODB_HOST=<droplet_ip> MONGODB_PORT=<port_that_redirects_in_docker_ps> NODE_ENV=production
    $ dokku config:set meanjs MONGODB_URL=mongodb://meanjs:T0VFUEwwRGhqZFpBTzhUUnVOcktXL3hrb3BnSG9rREJQeXhmS3lIWWEvUT0K@128.199.145.21:32769/meanjs-production
    

edit `config/env/production.js` locally:

    module.exports = {
      secure: true,
      port: process.env.MONGODB_PORT || 8443,
      db: {
        uri: process.env.MONGO_URI,
        options: {
          user: process.env.MONGODB_USERNAME,
          pass: process.env.MONGODB_PASSWORD
        },
      ...
    

run `grunt build` then push to dokku and voila! enjoy the fruits of your labor by visiting the address that appears at the end of deploy (we have to do this, because we haven&#8217;t configured a VHOST for it yet, so the port changes for each deploy).

[<img src="http://www.ygamretuta.info/wp-content/uploads/2015/08/Screen-Shot-2015-08-04-at-11.00.13-AM.png" alt="Screen Shot 2015-08-04 at 11.00.13 AM" class="alignnone size-medium wp-image-108" />][8]

 [1]: http://www.ygamretuta.info/wp-content/uploads/2015/08/Screen-Shot-2015-08-04-at-5.38.43-AM.png
 [2]: http://www.ygamretuta.info/wp-content/uploads/2015/08/Screen-Shot-2015-08-04-at-5.38.32-AM.png
 [3]: http://www.ygamretuta.info/wp-content/uploads/2015/08/Screen-Shot-2015-08-04-at-5.39.36-AM.png
 [4]: http://www.ygamretuta.info/wp-content/uploads/2015/08/Screen-Shot-2015-08-04-at-5.46.47-AM.png
 [5]: http://www.ygamretuta.info/wp-content/uploads/2015/08/Screen-Shot-2015-08-04-at-6.25.28-AM.png
 [6]: http://www.ygamretuta.info/wp-content/uploads/2015/08/Screen-Shot-2015-08-04-at-7.16.52-AM.png
 [7]: http://www.ygamretuta.info/wp-content/uploads/2015/08/Screen-Shot-2015-08-04-at-8.27.23-AM.png
 [8]: http://www.ygamretuta.info/wp-content/uploads/2015/08/Screen-Shot-2015-08-04-at-11.00.13-AM.png