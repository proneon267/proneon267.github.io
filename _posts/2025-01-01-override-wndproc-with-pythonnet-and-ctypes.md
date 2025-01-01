---
layout: post
title: Easily override wndproc with pythonnet and ctypes
description: Easily override wndproc with pythonnet and ctypes.
date: 2025-01-01
comments: true
blogstatus: active
---

It seems that directly overriding `WndProc()` method on the `Form` control in `WinForms`is not supported: [https://github.com/pythonnet/pythonnet/issues/2536](https://github.com/pythonnet/pythonnet/issues/2536). However, I have found that we can use win32 APIs directly to do so:
<hr>
{% highlight python %}
import clr

clr.AddReference("System.Windows.Forms")
clr.AddReference("System.Drawing")

from System.Windows.Forms import Form, Application
from System.Drawing import Size
import ctypes
from ctypes import wintypes

# Define necessary constants and functions
GWL_WNDPROC = -4
WM_CLOSE = 0x0010  # Example: close message

# Define SetWindowLongPtrW
windll = ctypes.WinDLL('user32', use_last_error=True)
SetWindowLongPtr = windll.SetWindowLongPtrW
SetWindowLongPtr.argtypes = [wintypes.HWND, wintypes.INT, ctypes.c_void_p]
SetWindowLongPtr.restype = ctypes.c_void_p

# Define GetWindowLongPtrW
GetWindowLongPtr = windll.GetWindowLongPtrW
GetWindowLongPtr.argtypes = [wintypes.HWND, wintypes.INT]
GetWindowLongPtr.restype = ctypes.c_void_p

# Define CallWindowProcW
CallWindowProc = windll.CallWindowProcW
CallWindowProc.argtypes = [
    ctypes.c_void_p,
    wintypes.HWND,
    wintypes.UINT,
    wintypes.WPARAM,
    wintypes.LPARAM,
]
CallWindowProc.restype = ctypes.c_long


class MyForm(Form):
    def __init__(self):
        super().__init__()
        self.Text = "WndProc Example"
        self.Size = Size(300, 200)

        # Get the window procedure (WndProc) for the window
        self.original_wndproc = GetWindowLongPtr(self.Handle.ToInt64(), GWL_WNDPROC)
        print(f"Original WndProc: {self.original_wndproc}")

        # Create a callback for the window procedure
        WNDPROC = ctypes.WINFUNCTYPE(
            ctypes.c_long,
            wintypes.HWND,
            wintypes.UINT,
            wintypes.WPARAM,
            wintypes.LPARAM,
        )

        # Register our WndProc to handle window messages
        self.wndproc_callback = WNDPROC(self.WndProc)
        SetWindowLongPtr(
            self.Handle.ToInt64(),
            GWL_WNDPROC,
            ctypes.cast(self.wndproc_callback, ctypes.c_void_p),
        )

        current_wndproc = GetWindowLongPtr(self.Handle.ToInt64(), GWL_WNDPROC)
        print(f"Current WndProc: {current_wndproc}")

    def WndProc(self, hwnd, msg, w_param, l_param):
        print(
            f"WndProc called: hwnd={hwnd}, msg={msg}, w_param={w_param}, l_param={l_param}"
        )

        # Handle specific message (for example, WM_CLOSE)
        if msg == WM_CLOSE:
            print("Window is closing.")
            return 0  # Prevent default close handling

        # Call the original window procedure for default handling
        return CallWindowProc(self.original_wndproc, hwnd, msg, w_param, l_param)


# Run the application and create an instance of the form
Application.Run(MyForm())

{% endhighlight %}
<hr>

That's it you are done :)
