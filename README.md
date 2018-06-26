# Chuwi-Hi-10-Plus-Linux-Support
*Support for running Linux on Chuwi Hi 10 Plus*

## Introduction
First of all I want to link this awesome guide I originally followed to get Linux working on my Chuwi Hi 10 Plus - [Linux On Hi10](https://github.com/danielotero/linux-on-hi10).
Fortunately, by now, new kernel versions were released and the out of box support is pretty decent.

So, after installing Linux (Lubuntu 4.15) I did have only **two** major issues remaining:

1. Backlight Support
1. Audio Support (only headphone jacket)
1. Screen Resolution
1. Touchscreen

## 1. Backlight Support
After some further research I found out, that when the kernel modules are loaded, i915 gets loaded before the PWM chip is initialized. This means that the PWM chip cannot be owned and therefore backlight isn't working.

### Fix: delay the loading of the kernel module i915
Therefore I've just added this line to the end of the following file **/etc/modprobe.d/blacklist.conf**:
**blacklist i915**
=> This will result in the kernel module i915 not being loaded at boot, which will in turn allow the PWM chip to be owned.

Now, we actually just want to delay the loading of this kernel module, not fully deactivate it, therefore we need to load this kernel module after all other modules.
For this specific purpose I've activated the **rc.local** for Ubuntu which is initially disabled (as you should use systemd instead) and just added the following line to **/etc/rc.local**:

sh /usr/local/bin/start_i915.sh

And the start_i915.sh script is defined as:

```shell
#!/bin/sh
modprobe i915
```

This script will be executed as root (by rc.local) before the login screen of Linux appears and load the i915 driver. Voilá, enjoy your working backlight.

## 2. Audio Support
Another problem I've faced was, that even with the above mentioned guide by @danielotero, neither my headphone jack nor my integrated speakers were working.

### Fix: blacklist sndi_hdmi_lpe_audio
There's a helpful discussion following this link https://github.com/danielotero/linux-on-hi10/issues/8 regarding sound, please feel free to check out this discussion as well. However, I decided to not follow the mentioned approach of manually compiling a  file and sticked to this:

Add another entry to **/etc/modprobe.d/blacklist.conf**:
blacklist sndi_hdmi_lpe_audio

After this you just need to download the following file **"bytcrrt5651.zip"**. I unfortunately do not know from which github discussion I got the link to those files, but however I will upload them at this repository as well, but please remember: **this is not created by me and I do not own those files**. If you know which courtesy this is, please leave a comment.
After you've downloaded the zip - file, extract it and move the two folders to **/usr/share/alsa/ucm** (replace the already existing directories if necessary).

=> In the end this worked out for me to have at least a working headphone jack - in case of any recommendations or further improvements leave me a comment.

## 3. Screen Resolution
As I was not happy with the original 1920x1200 resolution I downscaled to 1024x688.
Just execute the following commands to add this resolution:

```shell
xrandr --newmode "1024x688_60.00" 56.75  1024 1072 1176 1328  688 691 701 715 -hsync +vsync
xrandr --addmode DSI-1 1024x688_60.00
xrandr --output DSI-1 --mode "1024x688_60.00"m
```

Now you can just add it to a shell script and execute it every time your system starts.

## 4. Touchscreen
As I do currently use Lubuntu or i3wm I do not need to have the touchscreen enabled and working. If I want to do something with my touchscreen I just boot into my Windows on my Chuwi :). But if you do want to know how, please also refer to above mentioned guide.

# Help me!
I hope this guide was able to help you and you'll be able to enjoy your nice working Linux on the Chuwi Hi 10 Plus.
If you do think that anything I've written is not correct or only partially correct, please leave me a comment. I'm always eager to learn something new and I will definitely enjoy and incorporate your support!
