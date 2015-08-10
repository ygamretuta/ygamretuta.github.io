---
title: Client-side Validations for Rails 4.2
author: Ygam Retuta
layout: post
permalink: /client-side-validations-for-rails-4-2/
dsq_thread_id:
  - 3482979323
categories:
  - Ruby on Rails
tags:
  - ruby-on-rails
  - validations
---
The awesome client\_side\_validations gem for Ruby on Rails has not found its way on 4.2. That is, the original repo hasn&#8217;t. However, good folks from the community has gone ahead and made a fork of it; actively maintained and is compatible with RoR 4.2. If you are using a previous version of client\_side\_validations gem, just attach a `github` option to it that points to the active repo:

    gem 'client_side_validations', github: 'DavyJonesLocker/client_side_validations'

Rails&#8217; Turbolinks sometimes messes things up. To make it play nicely with `client_side_validations`, just add another plugin:

    gem 'client_side_validations-turbolinks', github: 'DavyJonesLocker/client_side_validations-turbolinks'

If you are using Rails&#8217; `data: {disable_with: 'text...'}`, then rest assured that long before the actively-maintained repo, an issue with it has been fixed. Just make sure to redirect after a successful form submit, with the usual http redirect or javascript.

Enjoy coding! 😀