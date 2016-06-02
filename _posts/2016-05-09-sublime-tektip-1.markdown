---
layout: post
title:  TekTip 1 - Sublime Text 3 Remapping Close Tab
description: Remapping the 'close tab' action in Sublime Text 3 to prevent frustration.
date:   2016-05-09 21:00:00 -0700
categories: tektip sublime text 3 keyboard mapping 
---

I often use Sublimes powerful replace tool to do mass editing (tempting the fates I know but that is what `git diff` and GitHub's review tools are for). I got really tiered of using the `command+w` feature to close all my tabs and once the last tab had closed it would close the window and thereby close my project. Here is my fix.

I am currently using Sublime Text 3 (build 3103). Default action for `command+w` is to close the current tab. The action it actually passes to sublime is `close`. You can see this in the default key bindings (I am not sure if it is possible but even if it is I reccomend NOT editing your default bindings, override them with user as described below):

`Sublime Text -> Preferences -> Key Bindings - Default`

Using this information we are able to override the default key binding with our own user binding.  To do this goto:

`Sublime Text -> Preferences -> Key Bindings - User`

Here is a template of a user key binding with only the fix for this concern:

{% highlight json %}
[
    { "keys": ["super+w"], "command": "close_file" }
]
{% endhighlight %}

I hope this is of use to all you fellow Sublimites. If you have any questions please feel free to reach out to me via the communication methods listed on this site.

Happy Coding!
