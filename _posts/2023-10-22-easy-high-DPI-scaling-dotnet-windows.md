---
layout: post
title: Easily set up High DPI scaling for Dotnet App on Windows
description: Easily set up High DPI scaling for Dotnet App on Windows.
date: 2023-10-22
comments: true
blogstatus: active
---

Here are 7 simple steps to set up High DPI scaling for a Dotnet App on Windows:

## Step 1: Declare DPI Awareness mode for the app
<hr>

These are 3 ways to do this:

### Via app config file: 

**Recommendation: Recommended**

Add the following to the manifest file to keep compatibility with Windows 10:
{% highlight XML %}
<compatibility xmlns="urn:schemas-microsoft-com:compatibility.v1">
  <application>
    <!-- Windows 10 compatibility -->
    <supportedOS Id="{8e0f7a12-bfb3-4fe8-b9a5-48fd50a15a9a}" />
  </application>
</compatibility>
{% endhighlight %}

Add the following to the app config file:
{% highlight XML %}
<configuration>
  <!-- ... other xml settings ... -->

  <System.Windows.Forms.ApplicationConfigurationSection>
    <add key="DpiAwareness" value="PerMonitorV2" />
    <add key="EnableWindowsFormsHighDpiAutoResizing" value="false" />
  </System.Windows.Forms.ApplicationConfigurationSection>

</configuration>
{% endhighlight %}

Call the following APIs at the earliest:
{% highlight C# %}
Application.EnableVisualStyles();
Application.SetCompatibleTextRenderingDefault(false);
{% endhighlight %}
<hr>

### Via app manifest file:

**Recommendation: Not Recommended**

Add the following to the manifest file:
{% highlight XML %}
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<assembly xmlns="urn:schemas-microsoft-com:asm.v1" manifestVersion="1.0" xmlns:asmv3="urn:schemas-microsoft-com:asm.v3">
  <asmv3:application>
    <asmv3:windowsSettings>
      <dpiAware xmlns="http://schemas.microsoft.com/SMI/2005/WindowsSettings">true</dpiAware>
      <dpiAwareness xmlns="http://schemas.microsoft.com/SMI/2016/WindowsSettings">PerMonitorV2</dpiAwareness>
    </asmv3:windowsSettings>
  </asmv3:application>
</assembly>
{% endhighlight %}

Call the following APIs at the earliest:
{% highlight C# %}
Application.EnableVisualStyles();
Application.SetCompatibleTextRenderingDefault(false);
{% endhighlight %}
<hr>

### Via APIs:

**Recommendation: Not Recommended**

For Windows Vista:
{% highlight C# %}
SetProcessDPIAware()
{% endhighlight %}

For Windows 8.1:
{% highlight C# %}
SetProcessDpiAwareness(PROCESS_PER_MONITOR_DPI_AWARE)
{% endhighlight %}

For Windows 10 version 1607 and above:
{% highlight C# %}
SetProcessDpiAwarenessContext(DPI_AWARENESS_CONTEXT_PER_MONITOR_AWARE_V2)
{% endhighlight %}

For the latest Dotnet Framework:

**Recommendation: Recommended**

{% highlight C# %}
Application.SetHighDpiMode(HighDpiMode.PerMonitorV2)
{% endhighlight %}

Call the following APIs at the earliest after calling the appropriate API from the above:
{% highlight C# %}
Application.EnableVisualStyles();
Application.SetCompatibleTextRenderingDefault(false);
{% endhighlight %}
<hr>

## Step 2: Get the DPI scale factor
<hr>

There are various ways to get the DPI scale factor, as given here: <https://proneon267.github.io/2023/10/18/all-the-ways-to-get-DPI-in-windows.html>

But the 3 recommended ways are:

### Using DeviceDpiNew:

**Reference**: <https://learn.microsoft.com/en-us/dotnet/api/system.windows.forms.dpichangedeventargs.devicedpinew>

{% highlight C# %}
OnDpiChanged (System.Windows.Forms.DpiChangedEventArgs e)
NewDpi = e.DeviceDpiNew
NewDpiScaleFactor = NewDpi / 96
{% endhighlight %}
<hr>

### Using GetDpiForWindow:

**Reference**: <https://learn.microsoft.com/en-us/windows/win32/api/winuser/nf-winuser-getdpiforwindow>

**Recommendation**: Recommended

{% highlight C# %}
NewDpi = GetDpiForWindow(Form.Handle())
NewDpiScaleFactor = NewDpi / 96
{% endhighlight %}
<hr>

### Using GetScaleFactorForMonitor

**Reference**: <https://learn.microsoft.com/en-us/windows/win32/api/shellscalingapi/nf-shellscalingapi-getscalefactorformonitor>

**Recommendation**: Recommended

** 
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

NewDpiScaleFactor = pScale / 100
{% endhighlight %}
<hr>

## Step 3: Use the DPI scale factor to draw elements
It is useful to store the original ScaleFactor at which the app was started. This will help in removing any cumulative effect of the scaling factor on the value of the element.

The DPI scaling is done in 2 steps:
{% highlight C# %}
scale_factor = NewDpiScaleFactor / OldDpiScaleFactor
new_value = old_value * scale_factor
{% endhighlight %}
<hr>

## Step 4: Use the DPI scale factor to draw fonts
It is useful to store the original font, as updating the font size requires the creating of a new font object. First, calculate the scale factor in the same way as for elements and then multiply it with the original font size.

{% highlight C# %}
scale_factor = NewDpiScaleFactor / OldDpiScaleFactor
new_value = old_value * scale_factor

NewFont = Font(
            original_font.FontFamily,
            original_font.Size * scale_factor,
            original_font.Style,
        )
{% endhighlight %}
<hr>

## Step 5: Add event handler to detect DPI changes
There are 4 main event handlers to detect DPI changes:

### DpiChangedAfterParent

**Reference**: <https://learn.microsoft.com/en-us/dotnet/api/system.windows.forms.control.dpichangedafterparent>

### DpiChangedBeforeParent

**Reference**: <https://learn.microsoft.com/en-us/dotnet/api/system.windows.forms.control.dpichangedbeforeparent>

### DpiChanged

**Reference**: <https://learn.microsoft.com/en-us/dotnet/api/system.windows.forms.form.dpichanged>

### SystemEvents.DisplaySettingsChanged

**Reference**: <https://learn.microsoft.com/en-us/dotnet/api/microsoft.win32.systemevents.displaysettingschanged>

{% highlight C# %}
SystemEvents.DisplaySettingsChanged += new EventHandler(OnDisplaySettingsChanged);    
{% endhighlight %}

Although it is recommended to use DpiChangedAfterParent, DpiChangedAfterParent, DpiChanged, but from testing SystemEvents.DisplaySettingsChanged seems to fire consistently when the DPI changes.
<hr>

## Step 6: Redraw elements and fonts on detecting a DPI change

On detecting a new DPI change, calculate the new scaling factor and use it to redraw elements and fonts in their appropriate sizes.

## Step 7: Done!

That's it you are done :)