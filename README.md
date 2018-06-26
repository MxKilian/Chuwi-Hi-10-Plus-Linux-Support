# Chuwi-Hi-10-Plus-Linux-Support
*Support for running Linux on Chuwi Hi 10 Plus*

## Introduction
First of all I want to link this awesome guide I originally followed to get Linux working on my Chuwi Hi 10 Plus - [Linux On Hi10](https://github.com/danielotero/linux-on-hi10).
Fortunately, by now, new kernel versions were released and the out of box support is pretty decent.

So, after installing Linux (Lubuntu) I did have only **two** major issues remaining:

* Backlight Support
* Audio Support 
* Screen Resolution

## Backlight Support
After some further research I found out, that when the kernel modules are loaded, i915 gets loaded before the PWM chip is initialized. This means that the PWM chip cannot be owned and therefore backlight isn't working.

### Fix - delay the loading of the kernel module i915
Therefore I've just added this line to the end of the following file **/etc/modprobe.d/blacklist.conf**:
**blacklist i915**
=> This will result in the kernel module i915 not being loaded at boot, which will in turn allow the PWM chip to be owned.

Now, we actually just want to delay the loading of this kernel module, not fully deactivate it, therefore we need to load this kernel module after all other modules.
For this specific purpose I've activated the **rc.local** for Ubuntu which is initially disabled (as you should use systemd instead) and just added the following line to **/etc/rc.local**:

sh /usr/local/bin/start_i915.sh

And the start_i915.sh script is defined as:

'''bash
#!/bin/sh
modprobe i915

## Audio Support




