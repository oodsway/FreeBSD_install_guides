### FreeBSD11+KDE Desktop Install Guide

This is a summary of commands shown in [Linux Video Tutorials](https://www.youtube.com/user/CloudSystemsEngineer/) YouTube video on how to install FreeBSD11 with KDE4 Desktop Enviroment in a VMware Virtual Machine. The guide begins after installtion of FreeBSD11 OS. The video link and reference information appear below.

[FreeBSD 11+KDE Desktop+Apps+VMware Tools](https://youtu.be/m-JExS3P-L4)

**Video Information**

```
Published on Sep 25, 2016
This video tutorial shows how to install FreeBSD 11 with KDE 4 Desktop and review on VMware Workstation step by step. We'll also 
install applications such as Nano, GIMP, Firefox and Gedit on FreeBSD 11. This tutorial also helps for installing FreeBSD 11 on 
physical computer or server. We'll also install VMware Tools (Open VM Tools) on FreeBSD 11 for better performance and usability 
(Fit Guest Now, Drag-Drop File and Mouse Integration).

Steps:
1- Download FreeBSD 11 ISO
2- Create VM on VMware Workstation/Player
3- Start Installation
4- Installing Xorg, KDE 4 Desktop
5- Installing Firefox, Nano, GIMP and gedit Applications on FreeBSD 11
6- Installing and Configuring VMware Tools (Open VM Tools)
7- FreeBSD 11 KDE Desktop Review
```

All credit belongs to **Linux Video Tutorials**. All mistakes are mine; use this guide at your own risk.


**After instaling FreeBSD11, reboot and login as root**

Confirm network connectivity (starts at video time [5:09](https://youtu.be/m-JExS3P-L4?t=309 )):

```# ping -c 3 www.google.com```

Update the system:

```# pkg update```

(say yes to install package management tool dialog)

```# pkg upgrade```

Install bash and an editor (e.g. nano):

```# pkg install bash nano```

Install Xorg windowning system (large download):

```# pkg install xorg```

Install KDE desktop (very large download):

```# pkg install kde```

_Note: I found bridged mode networking necessary vs NAT for my VM configuration to download desktop environment. YMMV._

Install gtk-oxygen-engine:

```# pkg install gtk-oxygen-engine```

Install kde-gtk-config:

```# pkg install kde-gtk-config```

Edit fstab:

```# nano /etc/fstab```

and append the following line, save and exit:

```proc     /proc       procfs  rw      0       0```

Edit /etc/rc.conf

```# nano /etc/rc.conf ```

Append these lines and save:

```
dbus_enable="YES"
hald_enable="YES"
kdm4_enable="YES"
```

Create .xinitrc

```# nano .xinitrc```

Append this line and save:

```exec /usr/local/bin/startkde```

Copy .xinitrc to user home directory:

```# cp .xinitrc /home/username/```
 
Change .xinitrc ownership to username:

```# chown username:username /home/username/.xinitrc```

Edit sudoers file :

```# nano /usr/local/etc/sudoers```

Below line for root ALL=(ALL) ALL, add the username to make a sudoer and save:

```NEW_SUDOER_USERNAME ALL=(ALL) ALL```

For use in VM install open-vm-tools:

```# pkg install open-vm-tools```

Edit /etc/rc.conf

```# nano /etc/rc.conf ```

Append these lines and save:

```
#VMware Tools
vmware_guest_vmblock_enable="YES"
vmware_guest_vmhgfs_enable="YES
vmware_guest_vmmemctl_enable="YES"
vmware_guest_vmxnet_enable="YES
vmware_guestd_enable="YES
```

Install vmware drivers:

```# pkg install xf86-video-vmware xf86-input-vmmouse```

Configure Xorg

```# Xorg -configure```

Confirm xorg.conf.new file exists in /root

```# ls```

Copy this file to /usr/local/etc/X11/xorg.conf.d/xorg.conf

```# cp xorg.conf.new /usr/local/etc/X11/xorg.conf.d/xorg.conf```

Edit xorg.conf

```# nano /usr/local/etc/X11/xorg.conf.d/xorg.conf```

In Section "ServerLayout", add the line:

```        Option          "AutoAddDevices" "Off"```
below the line:

```        InputDevice    "Keyboard0" "CoreKeyboard"```

resulting in:

```
Section "ServerLayout"
        Identifier     "X.org Configured"
        Screen      0  "Screen0" 0 0
        InputDevice    "Mouse0" "CorePointer"
        InputDevice    "Keyboard0" "CoreKeyboard"
        Option          "AutoAddDevices" "Off"
EndSection
```

In Section "InputDevice", change:

```        Driver      "mouse"```

to

```        Driver      "vmmouse"```

resulting in:

```
Section "InputDevice"
        Identifier  "Mouse0"
        Driver      "vmmouse"
        Option      "Protocol" "auto"
        Option      "Device" "/dev/sysmouse"
        Option      "ZAxisMapping" "4 5 6 7"
EndSection
```

In Section "Device", confirm or set Driver to:

```        Driver      "vmware"```

resulting in:

```
Section "Device"
        ### Available Driver options are:-
        ### Values: <i>: integer, <f>: float, <bool>: "True"/"False",
        ### <string>: "String", <freq>: "<f> Hz/kHz/MHz",
        ### <percent>: "<f>%"
        ### [arg]: arg optional
        #Option     "HWcursor"           	# [<bool>]
        #Option     "Xinerama"           	# [<bool>]
        #Option     "StaticXinerama"     	# <str>
        #Option     "GuiLayout"          	# <str>
        #Option     "AddDefaultMode"     	# [<bool>]
        #Option     "RenderAccel"        	# [<bool>]
        #Option     "DRI"                	# [<bool>]
        #Option     "DirectPresents"     	# [<bool>]
        #Option     "HWPresents"         	# [<bool>]
        #Option     "RenderCheck"        	# [<bool>]
	Identifier  "Card0"
	Driver      "vmware"
	BusID       "PCI:0:15:0"
EndSection
```

save these changes and then reboot:

```# reboot```

This should give the KDE login screen. 

**Enjoy your new VMware VM of FreeBSD11+KDE4!**


#### To Install FreeBSD11 with KDE4 on bare metal see this [FOSSKB guide](https://fosskb.in/2016/02/17/installing-kde-4-desktop-on-freebsd-11//)
