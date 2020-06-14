---
layout: post
title: Job for sssd.service failed because the control process exited with error code. 
date: 2020-02-05
comments: true
blogstatus: active
---
{% highlight %}
    Job for sssd.service failed because the control process exited with error code. 
    See “systemctl status sssd.service” and “journalctl -xe” for details.
{% endhighlight %}

{% highlight %}
    tail -f /var/log/sssd/sssd.log
{% endhighlight %}
 if you get
{% highlight %}
    pidfile exists at /var/run/sssd.pid
{% endhighlight %}
then run
{% highlight %}
    rm /var/run/sssd.pid
{% endhighlight %}
then try to start and enable sssd.service
{% highlight %}
    systemctl start sssd.service
    systemctl enable sssd.service
{% endhighlight %}
