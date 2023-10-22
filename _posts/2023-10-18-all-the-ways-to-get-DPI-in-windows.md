---
layout: post
title: All ways to get DPI in Windows using Dotnet Framework, Native APIs & Registry.
description: All the ways to get the DPI in Windows using Dotnet Framework, Native APIs & Registry.
date: 2023-10-18
comments: true
blogstatus: active
---

The following are all the ways to get the DPI on Windows using Dotnet, Native APIs & Registry keys.

## Using Dotnet and Native APIs:
<hr>

### Directly creating and using a Graphics object

**Graphics.DpiX Reference**: <https://learn.microsoft.com/en-us/dotnet/api/system.drawing.graphics.dpix>

{% highlight C# %}
graphics = Graphics.FromHwnd(IntPtr.Zero)
DpiX = graphics.DpiX
graphics.Dispose()

DPI = DpiX 
DPI_SCALE_FACTOR = DPI / 96
{% endhighlight %}

**Value Updation: After restarting the App**
<hr>

### By creating and using a Graphics object from a Control object
**Control Object Reference**: <https://learn.microsoft.com/en-us/dotnet/api/system.windows.forms.control>

**Graphics.DpiX Reference**: <https://learn.microsoft.com/en-us/dotnet/api/system.drawing.graphics.dpix>

{% highlight C# %}
graphics = Form.CreateGraphics()
DpiX = graphics.DpiX
graphics.Dispose()

DPI = DpiX
DPI_SCALE_FACTOR = DPI / 96
{% endhighlight %}

**Value Updation: After restarting the App**
<hr>

### Using DeviceDpi from a Control object
**Control Object Reference**: <https://learn.microsoft.com/en-us/dotnet/api/system.windows.forms.control>

**DeviceDpi Reference**: <https://learn.microsoft.com/en-us/dotnet/api/system.windows.forms.control.devicedpi>

{% highlight C# %}
DPI = Form.DeviceDpi 
DPI_SCALE_FACTOR = DPI / 96
{% endhighlight %}

**Value Updation: After restarting the App**
<hr>

### Using GetDeviceCaps
**Graphics Reference**: <https://learn.microsoft.com/en-us/dotnet/api/system.drawing.graphics>

**GetDeviceCaps Reference**: <https://webcache.googleusercontent.com/search?q=cache:https%3A%2F%2Fwww.pinvoke.net%2Fdefault.aspx%2Fgdi32.getdevicecaps>

{% highlight C# %}
graphics = Graphics.FromHwnd(IntPtr.Zero)
hdc = graphics.GetHdc()

HORZRES = GetDeviceCaps(hdc.ToInt32(), 8)
DESKTOPHORZRES = GetDeviceCaps(hdc.ToInt32(), 118)

graphics.ReleaseHdc(hdc)
graphics.Dispose()

DPI = (DESKTOPHORZRES / HORZRES) * 96
DPI_SCALE_FACTOR = (DESKTOPHORZRES / HORZRES)
{% endhighlight %}

**Value Updation: After restarting the App**
<hr>

### Using GetSystemDpiForProcess
**Reference**: <https://learn.microsoft.com/en-us/windows/win32/api/winuser/nf-winuser-getsystemdpiforprocess>

**Value Updation: Not Tested yet**
<hr>

### Using GetDpiForWindow
**Reference**: <https://learn.microsoft.com/en-us/windows/win32/api/winuser/nf-winuser-getdpiforwindow>

{% highlight C# %}
NewDpi = GetDpiForWindow(Form.Handle())
NewDpiScaleFactor = NewDpi / 96
{% endhighlight %}

**Value Updation: Not Tested yet**
<hr>

### Using GetDpiForSystem
**Reference**: <https://learn.microsoft.com/en-us/windows/win32/api/winuser/nf-winuser-getdpiforsystem>

**Value Updation: Not Tested yet**
<hr>

### Using DeviceDpiNew:

**Reference**: <https://learn.microsoft.com/en-us/dotnet/api/system.windows.forms.dpichangedeventargs.devicedpinew>

{% highlight C# %}
OnDpiChanged (System.Windows.Forms.DpiChangedEventArgs e)
NewDpi = e.DeviceDpiNew
NewDpiScaleFactor = NewDpi / 96
{% endhighlight %}
<hr>

### Using GetScaleFactorForMonitor
**MonitorFromRect Reference**: <https://learn.microsoft.com/en-us/windows/win32/api/winuser/nf-winuser-monitorfromrect>

**GetScaleFactorForMonitor Reference**: <https://learn.microsoft.com/en-us/windows/win32/api/shellscalingapi/nf-shellscalingapi-getscalefactorformonitor>

{% highlight C# %}
screen = Screen.PrimaryScreen
screen_rect = RECT(
    screen.Bounds.Left,
    screen.Bounds.Top,
    screen.Bounds.Right,
    screen.Bounds.Bottom,
)

hMonitor = MonitorFromRect(screen_rect, 2)
pScale = UINT()
GetScaleFactorForMonitor(hMonitor, &pScale)

DPI_SCALE = pScale / 100
{% endhighlight %}

**Value Updation: Instant**
<hr>

##### NOTE: 
It should be noted that some of these APIs report the correct values only when the app is running in various DPI aware modes only. Using them in non DPI aware mode apps will result in getting incorrect values.
<hr>

## Using Registry Keys:

<hr>

#### LogPixels:
The values of the key correspond to the DPI settings of the system:
{% highlight C# %}
HKEY_CURRENT_USER\Control Panel\Desktop\LogPixels
{% endhighlight %}

**Value Updation: Not sure as it is not present on the latest windows versions**
<hr>

#### AppliedDpi:
The value of the key changes according to the system DPI settings:
{% highlight C# %}
HKEY_CURRENT_USER\Control Panel\Desktop\WindowMetrics\AppliedDpi
{% endhighlight %}

**Value Updation: After signing out and resigning in**
<hr>

#### FontDpi:
The value of these keys changes according to the system DPI settings:
{% highlight C# %}
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\FontDPI
HKEY_CURRENT_USER\Software\Microsoft\Windows NT\CurrentVersion\FontDPI
{% endhighlight %}

**Value Updation: After signing out and resigning in**
<hr>

### DpiValue:
The value of the key changes according to the system DPI settings:
{% highlight C# %}
HKEY_CURRENT_USER\Control Panel\Desktop\PerMonitorSettings\\*UniqueString*\\DpiValue
{% endhighlight %}

**Value Updation: Instant**
<hr>

##### NOTE: 
It should be noted that these registry keys are not public facing APIs. As such, they can be removed or modified by Windows, without any prior warning or notice. Hence, it is not recommended to use these registry keys.
<hr>