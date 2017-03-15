---
layout: post
title:  Why pay for SSL? Using Let's Encrypt on a Heroku Rails App
description: Utilizing Let's Encrypts free SLL cert generation for a rails app hosted on Heroku.
date:   2016-05-19 08:45:00 -0700
categories: letsencrypt heroku rails ssl
---
I am a contributor to a non-profit's Open Source Software (OSS) project called [StockAid](https://github.com/on-site/StockAid).

Recently, when faced with handing the project over to the client, we realized our SSL (Secure Sockets Layer) certification had expired.  Luckily I was hanging out with [Mike Virta-Stone](https://github.com/smellsblue).  He let me know about [Let's Encrypt](https://letsencrypt.org/), a free, automated, and open certificate authority (CA), run for the public’s benefit. Let’s Encrypt is a service provided by the [Internet Security Research Group (ISRG)](https://letsencrypt.org/isrg/).

I was stoked to have found this solution for my client. Now, implimentation. I am working on a mac so I have access to [Homebrew](http://brew.sh/).

* Install letsencrypt using Homebrew:

{% highlight terminal %}
brew install letsencrypt
{% endhighlight %}

> If you are not on a mac feel free to grab the source:

{% highlight terminal %}
git clone https://github.com/letsencrypt/letsencrypt && cd letsencrypt
{% endhighlight %}

> Run the `letsencrypt-auto` command everywhere I use `letsencrypt`.  

* Run Let's Encrypt's certificate creation manually since we aren't on the server:

{% highlight terminal %}
sudo letsencrypt certonly --manual
{% endhighlight %}

* Enter your email.  
![Enter Domain Name]({{ site.url }}/images/letsencryptEmail.png)

* Agree to Terms of Service.  
![Enter Domain Name]({{ site.url }}/images/letsencryptTOS.png)

* Enter your domain name(s).  
![Enter Domain Name]({{ site.url }}/images/letsencryptDomainName.png)

* Confirm logging of IP (Internet Protocol) address (if you are comfortable with this).   
![Confirm IP Logging]({{ site.url }}/images/letsencryptIPLogging.png)

Next you'll see something like this **(Stop do the next step before pressing Enter)**:

{% highlight plaintext %}
Make sure your web server displays the following content at
http://blog.tekkedout.net/.well-known/acme-challenge/your_special_key_prefix before continuing:

your_special_key_output

If you don't have HTTP server configured, you can run the following
command on the target server (as root):

mkdir -p /tmp/letsencrypt/public_html/.well-known/acme-challenge
cd /tmp/letsencrypt/public_html
printf "%s" your_special_key_output > .well-known/acme-challenge/your_special_key_prefix
# run only once per server:
$(command -v python2 || command -v python2.7 || command -v python2.6) -c \
"import BaseHTTPServer, SimpleHTTPServer; \
s = BaseHTTPServer.HTTPServer(('', 80), SimpleHTTPServer.SimpleHTTPRequestHandler); \
s.serve_forever()"
Press ENTER to continue
{% endhighlight %}

>You'll need to follow the instruction provided accurately or the cert will not be generated.  

* Create a new controller (or use an existing one) to handle this request:

{% highlight ruby linenos %}
class LetsencryptController < ApplicationController
  skip_before_action :authenticate_user!

  def authenticate_key
    render text: "your_special_key_output"
  end
end
{% endhighlight %}

> I have devise so I had to add a skip_before_action to stop devise from preventing the page load

* Add route to the `routes.rb` file for your chosen controller:

{% highlight ruby %}
get "/.well-known/acme-challenge/:id" => "letsencrypt#authenticate_key"


{% endhighlight %}

* Test locally by going to localhost:3000/.well-known/acme-challenge/your_special_key_prefix and ensure your your_special_key_output is rendered.

* Add, commit, push, deploy using your methods for deployment

* Test live by going to http://<your_url>/.well-known/acme-challenge/your_special_key_prefix and ensure your your_special_key_output is rendered (if browser complains about old ssl cert just override for this load).

* Only once you confirm this is working properly should you return to the terminal and press `Enter`

* You should see this:

{% highlight plaintext %}
IMPORTANT NOTES:
 - Congratulations! Your certificate and chain have been saved at
   /etc/letsencrypt/live/blog.tekkedout.net/fullchain.pem.
   Your cert will expire on 2016-08-17. To obtain a new version of the
   certificate in the future, simply run Let's Encrypt again.
 - If you like Let's Encrypt, please consider supporting our work by:

   Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
   Donating to EFF:                    https://eff.org/donate-le
{% endhighlight %}

* **Update an existing cert** - Free Heroku account  


{% highlight terminal %}
sudo heroku certs:update /etc/letsencrypt/live/your_domain_name/fullchain.pem /etc/letsencrypt/live/your_domain_name/privkey.pem
{% endhighlight %}
  
* **Upload your first cert** - Free Heroku account  

{% highlight terminal %}
heroku addons:create ssl:endpoint
{% endhighlight %}

{% highlight terminal %}
sudo heroku certs:update /etc/letsencrypt/live/your_domain_name/fullchain.pem /etc/letsencrypt/live/your_domain_name/privkey.pem
{% endhighlight %}

* **Paid Heroku accounts**
  * I recommend checking out Heroku's recent blog post [Announcing Heroku Free SSL Beta and Flexible Dyno Hours](https://blog.heroku.com/archives/2016/5/18/announcing_heroku_free_ssl_beta_and_flexible_dyno_hours)

* Adding the SSL Endpoint changes the CNAME you need to point your custom domain to. You’ll need to edit your DNS using the new value listed in your Heroku dashboard for it to work correctly.

Hopefully this tutorial was of some use to you. If your still stuck please feel free to reach out to me via the communication methods listed on this site :D

Props to the post I originally followed from [Collective Idea](http://collectiveidea.com/blog/archives/2016/01/12/lets-encrypt-with-a-rails-app-on-heroku/).

Happy Coding!
