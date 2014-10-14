---
layout: post
title: "Django 1.7 Deploy on Heroku"
description: ""
category:
tags: []
---
{% include JB/setup %}

We will deploy a Django 1.7 project to Heroku. We will call it "shazam" It will have a "built-in" app called "members" which
will have a Member model that has a Cloudinary field. We will mainly work with an admin
interface so there will be no front-end.

I assume that you have already created a project, an app called "members", have already migrated initial schemas
and have already created a superuser and is able to access the admin interface.

Read [https://devcenter.heroku.com/articles/getting-started-with-django](this), as it will be a starting point:

Done? Now on to gotchas.

1. The guide uses earlier versions of Django, so instead of `python manage.py syncdb`, you should use `python manage.py migrate`.

    This is the Member model we will be working with:
    
    {% gist 619c29353109e0491744 %}

2. This is the requirements.txt we will use:

    {% gist 7d50839013eced6801f8 %}

3. The guide doesn't separate local settings from your production ones, so here's what we have to do. Create this file (`shazam/settings.py`):

    {% gist 0499163fa3fb01d48cd2 %}

    As you can see here this is one of the few approaches for django settings. Your `shazam/local_settings.py` can look like this:

    {% gist d1af79b5ea0f46530f3f %}

    That file will house your local database settings and development-only apps you can add later. Remember to add
    that file to your .gitignore file

4. Create a `static` directory under the `shazam` module and add a .keep file to it so git won't ignore it. This will house compiled assets on production.

    `mkdir -p static # shazam/static`

5. As for your WSGI, it will be as it is on Heroku

    {% gist f7c73dd35769aa671467 %}

6. We need to be able to display Cloudinary thumbnails on our list. Create or modify this file (`members/admin.py`):

    {% gist 5e89b7fc70486e820562 %}

7. We need our members app to also delete the photo in Cloudinary whenever a record is deleted (`members/signals.py`):

    {% gist 60acc62dd11822b39fb8 %}

8. We need to be able to automatically import our signals. Create this file (`members/apps.py`):

    {% gist 0d40e1572ca94c3472c1 %}

9. Then add this to your `shazam/__init__.py` file

    `default_app_config = 'members.apps.DefaultConfig'`

That's it. As you can see these are straightforward snippets and I can't really provide you with a project
with all the files in it for now. Maybe next time :P

Cheers and happy coding!