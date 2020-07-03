---
layout: post
title: Easily create bootable usb with ventoy
date: 2020-07-03
comments: true
blogstatus: active
---

So the other day, I got interested in a distro and wanted to try it out. Previous experience has taught me that creating a live bootable USB is tedious and erroneous. Yes, there is the Balena Etcher, but that would make my drive unusable on other OS. So, I searched and found the Ventoy project.

Ventoy will make your USB drive bootable. But here is the twist, you don't need to flash the iso file to the drive. Place the iso files onto the drive. Ventoy will make sure you can boot from it. Ventoy also supports multiple iso. Once ventoy configures the USB drive, you will also be able to use it as a regular drive.
* Install "fuse-exfat" as ventoy uses it
{% highlight bash %}
	sudo dnf install fuse-exfat
{% endhighlight %}
* Download Ventoy from 
{% highlight bash %}
        https://github.com/ventoy/Ventoy/releases
{% endhighlight %}
* Extract the zip and cd into the folder.
* Find out the USB device file path. 
{% highlight bash %}
	lsblk
{% endhighlight %}
Under the Name column, find out the device file name. It would look like "sd*". If in doubt, open "Disks" utility on fedora and find the device file path. 
* The file path would look like "/dev/sd*" 
* Run 
{% highlight bash %}
	sudo ./Ventoy2Disk.sh /dev/sd*
{% endhighlight %}
* Ventoy will warn twice that, "All data on the drive will be deleted!". 
* Enter 'y' and let ventoy configure the drive. 
* Place the iso files onto the drive.
* Boot from the drive and select the image to boot.

