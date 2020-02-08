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
	FontPath	"/usr/share/X11/fonts/misc"
	FontPath	"/usr/share/X11/fonts/100dpi/:unscaled"
	FontPath	"/usr/share/X11/fonts/75dpi/:unscaled"
	FontPath	"/usr/share/X11/fonts/Type1"
	FontPath	"/usr/share/X11/fonts/100dpi"
	FontPath	"/usr/share/X11/fonts/75dpi"
	FontPath	"/var/lib/defoma/x-ttcidfont-conf.d/dirs/TrueType"
EndSection
```

* Module - which modules to load

```text
Section "Module"
	Load	"bitmap"
	Load	"ddc"
	Load	"dri"
	Load	"extmod"
	Load	"freetype"
	Load	"glx"
	Load	"int10"
	Load	"type1"
	Load	"vbe"
EndSection
```

* InputDevice - keyboard and pointer \(mouse\)

```text
Section "InputDevice"
	Identifier	"Generic Keyboard"
	Driver		"kbd"
	Option		"CoreKeyboard"
	Option		"XkbRules"	"xorg"
	Option		"XkbModel"	"pc105"
	Option		"XkbLayout"	"us"
EndSection

Section "InputDevice"
	Identifier	"Configured Mouse"
	Driver		"mouse"
	Option		"CorePointer"
	Option		"Device"		"/dev/input/mice"
	Option		"Protocol"		"ImPS/2"
	Option		"Emulate3Buttons"	"true"
	Option		"ZAxisMapping"		"4 5"
EndSection

Section "InputDevice"
        Identifier      "Synaptics Touchpad"
        Driver          "synaptics"
        Option          "SendCoreEvents"        "true"
        Option          "Device"                "/dev/psaux"
        Option          "Protocol"              "auto-dev"
        Option		"RightEdge"		"5000"
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

* Monitor - display device description

```text
Section "Monitor"
	Identifier	"Generic Monitor"
	Option		"DPMS"
EndSection
```

* Device - video card description/information

```text
Section "Device"
	Identifier	"ATI Technologies, Inc. Radeon Mobility 7500 (M7 LW)"
	Driver		"radeon"
	BusID		"PCI:1:0:0"
	Option		"DynamicClocks"	"on"

	Option		"CRT2HSync"	"30-80"
	Option		"CRT2VRefresh"	"59-75"

  	Option		"MetaModes"	"1024x768 800x600 640x480 1024x768+1280x1024"

    Option  "XAANoOffscreenPixmaps"	"true"
EndSection

```

* Screen - binds a video adapter to a monitor

```text
Section "Screen"
    Identifier    "Screen0"
    Device        "Screen0 ATI Technologies, Inc. Radeon Mobility 7500 (M7 LW)"
    Monitor        "Generic Monitor"
    DefaultDepth    24
    SubSection "Display"
        Depth        1
        Modes        "1024x768"
    EndSubSection
    SubSection "Display"
        Depth        4
        Modes        "1024x768"
    EndSubSection
    SubSection "Display"
        Depth        8
        Modes        "1024x768"
    EndSubSection
    SubSection "Display"
        Depth        15
        Modes        "1024x768"
    EndSubSection
    SubSection "Display"
        Depth        16
        Modes        "1024x768"
    EndSubSection
    SubSection "Display"
        Depth        24
        Modes        "1024x768"
    EndSubSection
EndSection
```

* ServerLayout - binds one or more screens with one or more input devices

```text
Section "ServerLayout"
	Identifier	"DefaultLayout"
	Screen		"Default Screen"
	InputDevice	"Generic Keyboard"
	InputDevice	"Configured Mouse"
	InputDevice	"Synaptics Touchpad"
EndSection
```

### xwininfo



### xdpyinfo

### xhost

In the early days of X, configuring a display meant having extensive knowledge of the display’s capabilities and the ability to express information about not only resolution, but also horizontal and vertical sync values, color depth, and so on.



Since the advent of the Video Electronics Standards Association \(VESA\) and the Display Data Channel \(DDC\) protocols that allow a display to communicate these capabilities to a graphics card and thus to a computer, configuration has become much more automatic.

.

.

.

[https://developer.ibm.com/tutorials/l-lpic1-106-1/](https://developer.ibm.com/tutorials/l-lpic1-106-1/)

[https://en.wikipedia.org/wiki/X.Org\_Server](https://en.wikipedia.org/wiki/X.Org_Server)

[https://mg.pov.lt/xorg.conf](https://mg.pov.lt/xorg.conf)

.

