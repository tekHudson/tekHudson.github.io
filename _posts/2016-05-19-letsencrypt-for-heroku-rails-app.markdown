---
layout: post
title:  Why pay for SSL? Using Let's Encrypt on a Heroku Rails App
description: Utilizing Let's Encrypts free SLL cert generation for a rails app hosted on Heroku.
date:   2016-05-19 08:45:00 -0700
categories: letsencrypt heroku rails ssl
---
I am a contributor to a non-profit's Open Source Software (OSS) project called [StockAid](https://github.com/on-site/StockAid).

Recently, when faced with handing the project over to the client, the team realized our SSL (Secure Sockets Layer) certification had expired.  We decided to implement [Let's Encrypt](https://letsencrypt.org/), a free, automated (not working on Heroku yet), and open certificate authority (CA), run for the publics benefit. Let’s Encrypt is a service provided by the [Internet Security Research Group (ISRG)](https://letsencrypt.org/isrg/). I was stoked to have found this solution for my client.

Now, implementation. I am working on a mac so I have access to [Homebrew](http://brew.sh/).

Existing installations of Let's Encrypt skip to #2.

#### 1. Install Let's Encrypt (existing installations skip to #2)

{% highlight terminal %}
brew install letsencrypt
{% endhighlight %}

> If you are not on a mac feel free to grab the source:

{% highlight terminal %}
git clone https://github.com/letsencrypt/letsencrypt && cd letsencrypt
{% endhighlight %}

> Run the `letsencrypt-auto` command everywhere I use `letsencrypt`.

#### 2. Run Let's Encrypt's certificate creation manually since we aren't on the server

{% highlight terminal %}
sudo letsencrypt certonly --manual
{% endhighlight %}

#### 3. Setup Let's Encrypt (existing installations skip to #4)

I you are using Let's Encrypt for the first time it will ast a few setup questions.

Email address:
{% highlight terminal %}
Enter email address (used for urgent renewal and security notices) (Enter 'c' to
cancel):
{% endhighlight %}

TOS agreement:
{% highlight terminal %}
-------------------------------------------------------------------------------
Please read the Terms of Service at
https://letsencrypt.org/documents/LE-SA-v1.1.1-August-1-2016.pdf. You must agree
in order to register with the ACME server at
https://acme-v01.api.letsencrypt.org/directory
-------------------------------------------------------------------------------
(A)gree/(C)ancel:
{% endhighlight %}

Share email with Electronic Frontier Foundation:
{% highlight terminal %}
-------------------------------------------------------------------------------
Would you be willing to share your email address with the Electronic Frontier
Foundation, a founding partner of the Let's Encrypt project and the non-profit
organization that develops Certbot? We'd like to send you email about EFF and
our work to encrypt the web, protect its users and defend digital rights.
-------------------------------------------------------------------------------
(Y)es/(N)o:
{% endhighlight %}


#### 4. Certificate generation
{% highlight terminal %}
Please enter in your domain name(s) (comma and/or space separated)  (Enter 'c'
to cancel):
{% endhighlight %}


{% highlight terminal %}
Obtaining a new certificate
Performing the following challenges:
http-01 challenge for www.your-site-name.com
{% endhighlight %}


{% highlight terminal %}
-------------------------------------------------------------------------------
NOTE: The IP of this machine will be publicly logged as having requested this
certificate. If you're running certbot in manual mode on a machine that is not
your server, please ensure you're okay with that.

Are you OK with your IP being logged?
-------------------------------------------------------------------------------
(Y)es/(N)o:
{% endhighlight %}

Next you'll see something like this:

{% highlight plaintext %}
-------------------------------------------------------------------------------
Create a file containing just this data:

"<your_special_key_prefix>.<your_special_key>"

And make it available on your web server at this URL:

http://orders.gratefulgarment.org/.well-known/acme-challenge/<your_special_key_prefix>

-------------------------------------------------------------------------------
Press Enter to Continue
{% endhighlight %}

**STOP**

If you don't already have an endpoint on your application to receive verification from Let's Encrypt do step #5 _before_ pressing enter:

**Note**

If you install Let's Encrypt on a different computer <your_special_key> will be different which will require a change to your endpoint.

#### 5. Code changes required (existing code implemented skip to #6)
I created a new controller (alternatively you can use an existing one) to handle a request used by Let's Encrypt to validate ownership of the domain:

{% highlight ruby linenos %}
class LetsencryptController < ApplicationController
  skip_before_action :authenticate_user!

  def authenticate_key
    render text: "#{params[:id]}.<your_special_key>"
  end
end
{% endhighlight %}

> I have devise so I had to add a skip_before_action to stop devise from preventing the page load

Add a route to the `routes.rb` file pointing to your new endpoint:

{% highlight ruby %}
get "/.well-known/acme-challenge/:id" => "letsencrypt#authenticate_key"
{% endhighlight %}

Test locally by going to localhost:3000/.well-known/acme-challenge/<your_special_key_prefix> and ensure your output is rendered.

Test live by going to http://<your_url>/.well-known/acme-challenge/<your_special_key_prefix> and ensure your output is rendered (if browser complains about old ssl cert just override for this load).

#### 6. Certificate generation (cont.)
Only once you confirm this is working properly should you return to the terminal and press `Enter` and you should see something similar:

{% highlight plaintext %}
Waiting for verification...
Cleaning up challenges

IMPORTANT NOTES:
 - Congratulations! Your certificate and chain have been saved at:
   /etc/letsencrypt/live/<your_url>/fullchain.pem
   Your key file has been saved at:
   /etc/letsencrypt/live/<your_url>/privkey.pem
   Your cert will expire on XXXX-XX-XX. To obtain a new or tweaked
   version of this certificate in the future, simply run certbot
   again. To non-interactively renew *all* of your certificates, run
   "certbot renew"
 - If you like Certbot, please consider supporting our work by:

   Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
   Donating to EFF:                    https://eff.org/donate-le
{% endhighlight %}

#### 7. Upload new certificate

**First timers**
{% highlight terminal %}
heroku addons:create ssl:endpoint
{% endhighlight %}

{% highlight terminal %}
sudo heroku certs:update /etc/letsencrypt/live/your_domain_name/fullchain.pem /etc/letsencrypt/live/your_domain_name/privkey.pem
{% endhighlight %}

**Update an existing certificate**

{% highlight terminal %}
sudo heroku certs:update /etc/letsencrypt/live/your_domain_name/fullchain.pem /etc/letsencrypt/live/your_domain_name/privkey.pem
{% endhighlight %}


Hopefully this tutorial was of some use to you. If your still stuck please feel free to reach out to me via the communication methods listed on this site :D

Props to the post I originally followed from [Collective Idea](http://collectiveidea.com/blog/archives/2016/01/12/lets-encrypt-with-a-rails-app-on-heroku/).

Happy coding, stay weird!
