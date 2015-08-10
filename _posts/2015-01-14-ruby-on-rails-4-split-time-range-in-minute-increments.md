---
title: Ruby on Rails 4 Split Time Range in Minute Increments
author: Ygam Retuta
layout: post
permalink: /ruby-on-rails-4-split-time-range-in-minute-increments/
dsq_thread_id:
  - 3419132677
categories:
  - Ruby on Rails
tags:
  - ruby-on-rails
  - snippets
---
Here&#8217;s a short snippet to divide time ranges into 30-minute increments

    shift_start = Time.parse('8 am')
    shift_end = Time.parse('5 pm')
    shift = shift_start.to_i..shift_end.to_i
    timeslots = []
    shift.step(30.minutes){|t| timeslots << Time.at(t).strftime('%H:%M')}
    

output will be:

    ["08:00", "08:30", "09:00", "09:30", "10:00", "10:30", "11:00", "11:30", "12:00", "12:30", "13:00", "13:30", "14:00", "14:30", "15:00", "15:30", "16:00", "16:30", "17:00"]
    

## **what it does:**

We convert the time to integer so we can loop through it in set increments (in this case, 30 minutes). We then convert each increment to our desired time format and add that to our timeslots

## **what it&#8217;s useful for:**

As you may have probably noted in the variable names, it can be used in situations where we want to schedule tasks at a certain time within a particular range of time. Do this in 30 minutes, reserve 10:30 &#8211; 11:30 or something like that.

Cheers and have fun coding! 😀