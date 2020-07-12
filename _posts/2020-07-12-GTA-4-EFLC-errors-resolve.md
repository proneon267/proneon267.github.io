---
layout: post
title: Resolve GTA 4 and GTA Episodes from Liberty City(EFLC) errors
description: Resolve GTA 4 and GTA Episodes from Liberty City(EFLC) errors
date: 2020-07-12
comments: true
blogstatus: active
---

So the other day, I installed GTA 4 and GTA Episodes from liberty city(EFLC). Everything worked great on the GTA 4 but, things weren't so good on the EFLC side. First, I got the xlive.dll missing error and, after that, I got the xnetStartup error. But above all, the game lagged like hell even with 4GB of VRAM and 8GB of DRAM. But here is the twist I couldn't find any solution on any forum for this and, I ended up with the cleanest and simple answer.
* Download the Games for Windows Live(GFWL) from Microsoft.
* Download and extract the RAR file onto the EFLC game folder.
* Open commandline.txt file located in the game folder and add the following:
{% highlight bash %}
		-norestrictions
		-nomemrestrict
		-availablevidmem X
{% endhighlight %}
		where X is your VRAM. For example, for 2GB VRAM, do:
{%highlight bash%}
		-availablevidmem 2048
{% endhighlight %}
Done! 
