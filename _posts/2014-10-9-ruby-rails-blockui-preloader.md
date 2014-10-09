---
layout: post
title: "Ruby on Rails Block UI Preloader"
description: ""
category:
tags: []
---
{% include JB/setup %}

This covers your webpage in white and shows the preloader image at the center. The preloader image then disappears
after the page has finished loading.

Put the blockUI file (`jquery.blockUI.js` from the blockUI official page) in the `vendor/assets/javascripts` folder and
add this to your  `application.js` file:

```
//= require jquery.blockUI
```

From there you can create a coffescript (here I named it pages.js.coffee.erb). Do not forget the .erb extension
because that will allow us to use asset helpers in our coffeescript.

{% gist 5aa2c285cce4bffb0a2a%}

Note that you can change the overlay or the overlay opacity, the border colors to whatever you prefer.

The `spinner.gif` file can also be replaced with any preloader images you prefer.

Happy coding!