---
layout: post
title: Fedora 31 Post-Installation
date: 2020-02-05
comments: true
blogstatus: active
---
I know you're thinking, why the bell isn't this a script? To which I would answer, running random scripts found on the internet can be lethal for your pc, I have experienced this and regret to this day. So be safe run each line manually.
* Change DNF config:
~~~
    nano /etc/dnf/dnf.conf	;Edit /etc/dnf/dnf.conf and add the two lines:
        fastestmirror=true
        deltarpm=true
~~~
* Add RPM Fusion repo and refresh the list:
~~~
    dnf update --refresh
    dnf install https://download1.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm 
    dnf install https://download1.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm
~~~
* update the distro first:
~~~
    dnf upgrade
~~~
* Add multimedia codecs:
~~~
    sudo dnf install \
    gstreamer-plugins-base \
    gstreamer1-plugins-base \
    gstreamer-plugins-bad \
    gstreamer-plugins-ugly \
    gstreamer1-plugins-ugly \
    gstreamer1-plugins-good \
    gstreamer1-plugins-good-extras \
    gstreamer1-plugins-bad-freeworld \
    ffmpeg \
    gstreamer-ffmpeg
~~~
* Setup system to be able to compile stuff (even if you are not a dev):
~~~
    sudo yum groupinstall "Development Tools" "Development Libraries"
    sudo dnf install @development-tools
    yum install "kernel-devel-uname-r == $(uname -r)"
    dnf install dkms lshw
~~~
* Replace the stock wifi driver because it doesn't work:
~~~
    git clone https://github.com/Mange/rtl8192eu-linux-driver           ;Clone the driver repo
    cd rtl8192eu-linux-driver                                           ;Go to the cloned repo directory
    sudo dkms add .                                                     ;Add the driver to DKMS
    sudo dkms install rtl8192eu/1.0                                     ;Build and install driver
    echo "blacklist rtl8xxxu" | sudo tee /etc/modprobe.d/rtl8xxxu.conf  ;Blacklist the stock driver
    echo -e "8192eu\n\nloop" | sudo tee /etc/modules                    ;Force to activate from boot
    
    Regenerate grub config and initramfs:
        sudo grub2-mkconfig -o /boot/efi/EFI/fedora/grub.cfg    ;For UEFI systems
        sudo grub2-mkconfig -o /boot/grub2/grub.cfg             ;For BIOS systems
        dracut -v -f                                            ;Regenerate initramfs
        
    systemctl reboot -i                                                 ;Reboot the system
    sudo lshw -c network                                                ;Should see the line driver=8192eu
~~~
* Install additional packages:
~~~
    dnf install dnfdragon \         ;Install & uninstall softwares
    p7zip \                         ;Compression tool
    libreoffice \                   ;Office suite
    persepolis \                    ;Fast download manager
    kate \                          ;Advanced text editor
~~~
* Remove preinstalled softwares:
~~~
    dnf remove falkon \     ;Why would anyone even include 3 browsers in one distro?
    kget \                          ;Just doesn't work and is slow
    konqueror \                     ;Firefox is already included 
~~~
* Other Recommended Softwares:
~~~
 Stacer >>  System cleaner
 Gravit designer    >>  Not free but still great vector art tool
 brave-browser >> Best new google chrome alternative
~~~
* Browser Extensions:
~~~
AdBlocker Ultimate >> Block all ads, no acceptable ads
Lastpass    >>  Great free password manager
~~~
