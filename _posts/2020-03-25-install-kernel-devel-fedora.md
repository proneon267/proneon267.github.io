---
layout: post
title: How to install the correct kernel-devel in fedora?
date: 2020-03-25
comments: true
blogstatus: active
---
While you could install the kernel-devel package with 

{% highlight %}
sudo dnf install kernel-devel
{% endhighlight %}

but this sometimes leads to the installation of an incompatible or incorrect package of kernel-devel. 

The best and neat way is to run simply:
 
{% highlight %}
sudo yum install "kernel-devel-uname-r == $(uname -r)"
{% endhighlight %}

This will install the compatible and required version of the kernel-devel package in fedora.
