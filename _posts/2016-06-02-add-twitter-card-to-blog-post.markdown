---
layout: post
title: Pretify your Jekyll Twitter Shares
description: Utilizing Twitter Cards to enhance the display of links to your pages.
date:   2016-06-02 08:30:00 -0700
categories: twitter card blog post
---
Ever wanted to have your, or others, Twitter posts containing links to your blog look a bit snazzier?  

![Improved Twitter Post]({{ site.url }}/images/improvedTwitterPost.png)

Here is the [Twitter dev site](https://dev.twitter.com/cards/overview) for this feature. I needed more information so I scoured the web and combined information and came up with these simple steps:

> If you are using a default Jekyll project then just follow the instructions.  
> If not you may need to put code snippets in your corrisponding locations.

* Choose the image you want to have displayed on the Twitter post. This depends on which type of card you want:
  * Summary Card (Used for this demo) - At least 120px by 120px and no more than 1MB in size. For an expanded tweet and its detail page, the image will be cropped to a 4:3 aspect ratio and resized to be displayed at 120px by 90px. The image will also be cropped and resized to 120px by 120px for use in embedded tweets.
  * Summary Card with Large Image - At least 280px by 150px, and no more than 1MB in size.
  * Photo Card - All files must be below 1MB in size. Twitter will resize images, maintaining the original aspect ratio to fit the following maximum sizes:
    * Web: 375px by 435px
    * Mobile with a non-retina display375px by 280px
    * Mobile with a retina display 750px by 560px
  * Gallery Card - No exact size is specified, but aim for 200px by 200px as a minimum. Images cannot exceed 1MB in size.
  * App Card - App logo generated from your app ID. Image should be at least 800px by 320px, and can be in jpg, jpeg, png, or gif format.
  * Player Card - See [this page](https://dev.twitter.com/cards/types/player) for more info
  * Product Card - 160px by 160px or greater with a 1:1 aspect ratio
  * Lead Generation Card - Should be at least 800px wide by 200px tall with a 4:1 aspect ratio. The maximum image size is 3 MB and you can crop and resize images after they’ve been uploaded.  Images can be in jpg, jpeg, png, or gif format.
  * Website Card - 800px by 320px and the image must be less than 3MB in size.
  * Coming Soon: Video Card - ???

* Place it into your images folder.
  * If you don't have an images folder make a folder named `images` in your root project directory and put it there.
* Add the following code to your `_includes/head.html`

{% highlight html linenos %}
{% raw %}
<meta name="twitter:card" content="summary" />
<meta name="twitter:site" content="@TekHudson" />
<meta name="twitter:title" content="{% if page.title %}{{ page.title | escape }}{% else %}{{ site.title | escape }}{% endif %}" />
<meta name="twitter:description" content="{% if page.description %}{{ page.description | escape }}{% else %}{{ site.description | escape }}{% endif %}" />
<meta name="twitter:image" content="http://blog.tekkedout.net/images/blogIcon.png" />
{% endraw %}
{% endhighlight %}

* You may notice the use of `page.title` and `page.description` in this code. You probably already have a `title` on all your posts. The `description` will likely need to be added to any post you want to utilize the card as this is a required field for it to function correctly on Twitters end.

{% highlight yml linenos %}
---
layout:      post
title:       Pretify Your Jekyll Twitter Shares
description: Utilizing Twitter Cards to enhance the display of links to your pages.
date:        2016-05-23 19:30:00 -0700
categories:  twitter card blog post enhancement
---
{% endhighlight %}

And viola, when you link to your posts from twitter you should see the example image.

If you have any quesitons or concerns please feel free to reach out to me via the communication methods listed on this site :D

Happy Coding!
