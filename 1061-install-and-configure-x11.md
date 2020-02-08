# 106.1. Install and configure X11

## **106.1 Install and configure X11**

**Weight:** 2

**Description:** Candidates should be able to install and configure X11.

**Key Knowledge Areas:**

* Verify that the video card and monitor are supported by an X server
* Awareness of the X font server
* Basic understanding and knowledge of the X Window configuration file

**Terms and Utilities:**

* /etc/X11/xorg.conf
* xhost
* DISPLAY
* xwininfo
* xdpyinfo
* X

In the days of very expensive computers that were shared among many users, X terminals provided a low cost way for many users to share the resources of a single computer. Nowadays computers have become as much powerful  that no one doesn't think about sharing resources, but there are some history lessons which should know about!

### X

The **X Window System** \(also known as X11, or simply X\) is a client/server windowing system for bitmap displays.

The **X server** is the program or dedicated terminal that displays the windows and handles input devices such as keyboards, mice, and touchscreens. The clients are applications.

**X** is designed to be network transparent, so that an X server can display windows from local or networked application sources.

{% hint style="info" %}
**XOrg** Server is the free and open-source implementation of the display server for the X Window System managed by the X.Org Foundation.
{% endhint %}

### /etc/X11/xorg.conf

The file xorg.conf is a file used for configuring the X.Org Server. xorg.conf usually is located in /etc/X11/xorg.conf but that does not exist any more on modern linux distributions , so we use a sample xorg.conf to explain that. 

The xorg.conf configuration file is  organized into sections which may be specified in any order. The general section format is

> Section "SectionName" 
>
>        SectionEntry ... 
>
> EndSection

Lets take a quick look at most important ones:

* Files - pathnames for files such as fontpath

```text
Section "Files"
        ModulePath   "/usr/lib64/xorg/modules"
        FontPath     "/usr/share/fonts/misc:unscaled"
        FontPath     "/usr/share/fonts/Type1/"
        FontPath     "/usr/share/fonts/100dpi:unscaled"
        FontPath     "/usr/share/fonts/75dpi:unscaled"
        FontPath     "/usr/share/fonts/ghostscript/"
        FontPath     "/usr/share/fonts/cyrillic:unscaled"
        FontPath     "/usr/share/fonts/misc/sgi:unscaled"
        FontPath     "/usr/share/fonts/truetype/"
        FontPath      "built-ins"
EndSection
```

* Module - which modules to load

```text
Section "Module"
        Load   "glx"
        Load   "vnc"
EndSection
```

* InputDevice - keyboard and pointer \(mouse\)

```text

Section "InputDevice"
        Identifier  "Keyboard0"
        Driver      "kbd"
EndSection

Section "InputDevice"
        Identifier  "Mouse0"
        Driver      "mouse"
        Option      "Protocol" "auto"
        Option      "Device" "/dev/input/mice"
        Option      "ZAxisMapping" "4 5 6 7"
EndSection
```

* Monitor - display device description

```text
Section "Monitor"
        Identifier   "Monitor0"
        VendorName   "Monitor Vendor"
        ModelName    "Monitor Model"
EndSection
```

* Device - video card description/information

```text
Section "Device"
        ### Available Driver options are:-
        ### Values: <i>: integer, <f>: float, <bool>: "True"/"False",
        ### <string>: "String", <freq>: "<f> Hz/kHz/MHz",
        ### <percent>: "<f>%"
        ### [arg]: arg optional
        #Option     "SWcursor"             # [<bool>]
        #Option     "HWcursor"             # [<bool>]
        #Option     "NoAccel"              # [<bool>]
        #Option     "ShadowFB"             # [<bool>]
        #Option     "VideoKey"             # <i>
        #Option     "WrappedFB"            # [<bool>]
        #Option     "GLXVBlank"            # [<bool>]
        #Option     "ZaphodHeads"          # <str>
        #Option     "PageFlip"             # [<bool>]
        #Option     "SwapLimit"            # <i>
        #Option     "AsyncUTSDFS"          # [<bool>]
        #Option     "AccelMethod"          # <str>
        #Option     "DRI"                  # <i>
        Identifier  "Card0"
        Driver      "nouveau"
        BusID       "PCI:1:0:0"
EndSection
```

* Screen - binds a video adapter to a monitor

```text
Section "Screen"
        Identifier "Screen0"
        Device     "Card0"
        Monitor    "Monitor0"
        SubSection "Display"
                Viewport   0 0
                Depth     1
        EndSubSection
        SubSection "Display"
                Viewport   0 0
                Depth     4
        EndSubSection
        SubSection "Display"
                Viewport   0 0
                Depth     8
        EndSubSection
        SubSection "Display"
                Viewport   0 0
                Depth     15
        EndSubSection
        SubSection "Display"
                Viewport   0 0
                Depth     16
        EndSubSection
        SubSection "Display"
                Viewport   0 0
                Depth     24
```

* ServerLayout - binds one or more screens with one or more input devices

```text
Section "ServerLayout"
        Identifier     "X.org Configured"
        Screen      0  "Screen0" 0 0
        InputDevice    "Mouse0" "CorePointer"
        InputDevice    "Keyboard0" "CoreKeyboard"
EndSection
```

In the early days of X, configuring a display meant having extensive knowledge of the display’s capabilities and the ability to express information about not only resolution, but also horizontal and vertical sync values, color depth, and so on.



Since the advent of the Video Electronics Standards Association \(VESA\) and the Display Data Channel \(DDC\) protocols that allow a display to communicate these capabilities to a graphics card and thus to a computer, configuration has become much more automatic.

.

.

.

[https://developer.ibm.com/tutorials/l-lpic1-106-1/](https://developer.ibm.com/tutorials/l-lpic1-106-1/)

[https://en.wikipedia.org/wiki/X.Org\_Server](https://en.wikipedia.org/wiki/X.Org_Server)

.

