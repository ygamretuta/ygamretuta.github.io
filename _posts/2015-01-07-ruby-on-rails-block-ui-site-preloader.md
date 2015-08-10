---
title: Ruby on Rails Block UI Site Preloader
author: Ygam Retuta
layout: post
permalink: /ruby-on-rails-block-ui-site-preloader/
dsq_thread_id:
  - 3398270501
categories:
  - Ruby on Rails
tags:
  - ruby-on-rails
---
This covers your webpage in white and shows the preloader image at the center. The preloader image then disappears after the page has finished loading.

Put the blockUI file (`jquery.blockUI.js` from the blockUI official page) in the `vendor/assets/javascripts` folder and add this to your `application.js` file:

`//= require jquery.blockUI`

From there you can create a coffescript (here I named it `pages.js.coffee.erb`). Do not forget the .erb extension because that will allow us to use asset helpers in our coffeescript.

    $(->
      $.blockUI
        message: '<img src="<%= image_path('spinner.gif') %>"/>'
        css:
            border: '0px solid #fff'
            cursor: 'wait'
            backgroundColor: '#fff'
        overlayCSS:
            backgroundColor: '#fff'
            opacity: 10
            cursor: 'wait'
    
        $.ajax
            complete: (data) ->
                $.unblockUI()
                return
    )
    

Note that you can change the overlay or the overlay opacity, the border colors to whatever you prefer.

The `spinner.gif` file can also be replaced with any preloader images you prefer.

To see it in action, you can visit <http://ulife.herokuapp.com/>

Happy coding!