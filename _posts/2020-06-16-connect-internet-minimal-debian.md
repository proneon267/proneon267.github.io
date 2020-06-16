---
layout: post
title: Connect to internet with minimal debian linux
date: 2020-06-16
comments: true
blogstatus: active
---

The other day, I wanted to install Debian on my old machine. But past experiences have taught me, downloading the full iso image will be a wastage of bandwidth & space. So, I went for the net installer image. 
But here is the twist, I didn't connect to the internet while installing. 
And hence, I ended up with a minimal system.

I didn't have an ethernet cable, so I decided to use USB tethering from my phone. This minimal system hadn't been configured to connect to use this network interface. So, here's how I connected to the internet with minimal Debian installation.

List the available network interfaces:
{% highlight bash %}
  ip link list
{% endhighlight %}
Then plug in your device and re-run the above command. You will see a new interface added to the list. 
Note the device id. As an example, let the device id be **ens0**.

Now edit {% highlight bash %} /etc/network/interfaces {% endhighlight %}
Add the following to the file:
{% highlight bash %}
iface ens0 inet dhcp
{% endhighlight %}
Then as root, run the following:
{% highlight bash %}
ifdown ens0
ifup ens0
{% endhighlight %}
To check your connection, run ping on google
{% highlight bash %}
ping google.com
{% endhighlight %}
Now you are connected and ready to install what you want on the minimal Debian installation.
