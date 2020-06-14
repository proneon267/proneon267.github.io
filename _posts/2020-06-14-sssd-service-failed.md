---
layout: post
title: Job for sssd.service failed because the control process exited with error code. 
date: 2020-02-05
comments: true
blogstatus: active
---
{% highlight bash %}
    Job for sssd.service failed because the control process exited with error code. 
    See “systemctl status sssd.service” and “journalctl -xe” for details.
{% endhighlight %}

{% highlight bash %}
    tail -f /var/log/sssd/sssd.log
{% endhighlight %}
 if you get
{% highlight bash %}
    pidfile exists at /var/run/sssd.pid
{% endhighlight %}
then run
{% highlight bash %}
    rm /var/run/sssd.pid
{% endhighlight %}
or if you get 
{% highlight bash %}
    Cannot open '/var/log/sssd/sssd.log' for reading: No such file or directory
{% endhighlight %}
then run 
{% highlight bash %}
    mkdir /var/log/sssd
    systemctl -i reboot
{% endhighlight %}
then try to start and enable sssd.service
{% highlight bash %}
    systemctl start sssd.service
    systemctl enable sssd.service
{% endhighlight %}
