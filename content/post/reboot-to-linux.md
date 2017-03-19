+++
Categories = ["Development"]
Tags = ["Development", "Windows", "Linux"]
date = "2017-03-18T10:14:19-06:00"
title = "Reboot to Linux"

+++

I dual boot my main PC with Windows 10, and Arch Linux. The reason for this is that I
like to use Windows for gaming, and general day to day tasks, i.e. email, internet, etc,
but for any kind of development work I prefer to use Linux.

In the past I did this by rebooting and then using the UEFI firmware settings to change 
the boot device to the one containing my Arch install. I went this route instead of using
GRUB, or the Windows Boot Manager, so that my family never has
to deal with the dual boot. For them it always boots to Windows, and I don't worry about
them ending up in Linux by accident. The downside of this is for me is that I have to make 
sure to hit the special key, i.e. F12, to bring up the boot selection menu so I can select Arch.
So instead I've written a little batch script to reboot from Windows and go into Arch,
though you could replace Arch with any Linux distro, or other OS.

The goal of the batch file is to have an icon on my Windows desktop that when run it will change
my default boot device to Arch for the next boot only, and then reboot the PC. Any future boots
after the next one should revert back to Windows 10.

Here is the batch file I came up with:
{{< gist rivernate 37efb7508629bfec313710f2ca07ecea >}}

This just turns of echoing commands in the file:

{{< highlight batch>}}
@echo off
{{</ highlight>}}

This checks to see if we are running as an admin, and if not prompts to run as an admin:

{{< highlight batch>}}
if not "%1"=="am_admin" (powershell start -verb runas '%0' am_admin & exit)
{{</ highlight>}}

This is where the magic happens:

{{< highlight batch>}}
bcdedit /set {fwbootmgr} bootsequence {uuid}
{{</ highlight>}}

We are using [bcdedit](https://technet.microsoft.com/en-us/library/cc709667(v=ws.10).aspx)
to change the bootsequence temporarily to boot Arch instead of Windows. You will need to 
change the **"{uuid}"** to the correct uuid of your device. In order to find this you can 
run the following from an admin command prompt: 

{{< highlight batch>}}
bcdedit /enum firmware
{{</ highlight>}}

You should get something that looks like this:
{{< figure src="/images/reboot-to-linux/bcdedit-list.png" title="List Firmware Boot Devices" >}}
In my case the Firmware Application is my Arch install, So I will replace 
the **"{uuid}"** with the uuid starting with "30b0b529"

The **bootsequence** option:

>Specifies a one-time display order to be used for the next boot. This command is similar to the /displayorder option, except that it is used only the next time the computer starts. Afterwards, the computer reverts to the original display order.

The final step is to reboot:
{{< highlight batch>}}
shutdown /r /t 0
{{</ highlight>}}

Now I have a simple script that when I click on it will reboot me directly into my Arch Linux install, without
making any permanent changes to my boot setup. For some niceties I put the actual script in a folder in my 
users directory, then created a shortcut to it that I placed on my desktop with a custom icon.


