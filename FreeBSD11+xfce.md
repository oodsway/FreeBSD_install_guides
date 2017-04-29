### FreeBSD11+Xfce4 Desktop Install Guide

This is a summary of commands shown in [Linux Video Tutorials](https://www.youtube.com/user/CloudSystemsEngineer/) YouTube video on how to install FreeBSD11 with Xfce4 Desktop Enviroment in a VMware Virtual Machine. The guide begins after installtion of FreeBSD11 OS. The video link and reference information appear below.

[FreeBSD11+XFCE Desktop+Apps+VMware Tools](https://youtu.be/j3isaoJsHDU)

**Video Information**

```
Published on Sep 24, 2016
This video shows how to install FreeBSD 11 with XFCE 4 Desktop Environment and review on 
VMware Workstation or VMware Player step by step. We'll install applications such as Nano 
text editor, GIMP image editor, Firefox web browser, VLC media player and gedit text editor 
on FreeBSD 11. We'll also install VMware Tools (Open VM Tools) on FreeBSD 11 for better
performance and usability (Fit Guest Now, Drag-Drop File features and Mouse Integration). 
This tutorial also helps for installing FreeBSD 11 on physical computer or bare-metal 
server.

Steps:
1- Download FreeBSD 11 ISO
2- Create VM on VMware Workstation/Player
3- Start Installation
4- Install Xorg, Xfce4 and Slim Login Manager
5- Install Applications such as Firefox, Nano, GIMP, VLC and gedit
6- Install and Configure VMware Tools (Open VM Tools)
7- FreeBSD 11 Xfce Review
```

All credit belongs to **Linux Video Tutorials**. All mistakes are mine; use this guide at your own risk.


**After instaling FreeBSD11, reboot and login as root**

Confirm network connectivity with ping:

```# ping -c 3 www.google.com```

Update the system:

```# pkg update```

(say yes to install package management tool dialog)

```# pkg upgrade```

Install bash and an editor (e.g. nano):

```# pkg install bash nano```

Install Xorg windowning system (large download):

```# pkg install xorg```

Install Xfce desktop (large download):

```# pkg install xfce```

_Note: I found bridged mode networking necessary vs NAT for my VM configuration to download desktop environment. YMMV._

Install Slim (login manager):

```# pkg install slim```

Edit /etc/rc.conf

```# nano /etc/rc.conf ```

Append these lines and save:

```
dbus_enable="YES"
hald_enable="YES"
#Enable Slim Login Manager
slim_enable="YES"
```
Create .xinitrc

```# nano .xinitrc```

Append this line and save:

```exec startxfce4```

Copy .xinitrc to user home directory:

```# cp .xinitrc /home/username/```

Change .xinitrc ownership to username (this was step omitted in this video was included in KDE video):

```# chown username:username /home/username/.xinitrc```

Install sudo package:

```# pkg install sudo```

Edit sudoers file:

```# nano /usr/local/etc/sudoers```

Below line for root ALL=(ALL) ALL, add the username to make a sudoer and save:

```NEW_SUDOER_USERNAME ALL=(ALL) ALL```

Install Applications (Completely Optional at this stage):

```# pkg install firefox gimp gedit vlc```

For use in VM install open-vm-tools:

```# pkg install open-vm-tools```

Install vmware drivers:

```# pkg install xf86-video-vmware xf86-input-vmmouse```

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
        #Option     "ShadowFB"                  # [<bool>]
        #Option     "DefaultRefresh"            # [<bool>]
        #Option     "ModeSetClearScreen"        # [<bool>]
        Identifier  "Card0"
        Driver      "vmware"
        BusID       "PCI:0:15:0"
EndSection
```

save these changes and then reboot:

```# reboot```

This should give the Xfce4 login screen. After login select 

```Use default config``` 

at the panel dialog.

**Enjoy your new VMware VM of FreeBSD11+Xfce4**



#### To Install FreeBSD11 with Xfce4 on Bare Metal [See](https://fosskb.in/2016/03/17/installing-xfce-4-on-freebsd-11/):

[FROM FREE AND OPEN SOURCE KNOWLEDGE BASE](https://fosskb.in/2016/03/17/installing-xfce-4-on-freebsd-11/)