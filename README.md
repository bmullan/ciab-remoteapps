#                                        ciab-remoteapps

### Configuration Steps to Implement CIAB RemoteApps
### June 2024 - Brian Mullan 
### (bmullan.mail@gmail.com)



**Prereq:**

Two ways to enable remoteapps for use on Ubuntu Desktop.

If you have an existing Windows 11 pc/laptop that is connected on the LAN with your Ubuntu Desktop system then you can use that.  
All the same steps can be used except anything mentioning LXD/LXC or Incus

However many will need/want to use an LXD or Incus Windows 11 VM with all Windows apps you need installed (you can always install more later).

Assume your Windows 11 VM is named - **win11 \**
and currently in a Stopped state (ie not running)

**If it is an LXD VM:**

You can set the # of CPU and the amount of memory for the VM with the following commands:

**$ lxc config set win11 limits.cpu=N**     (N=1,2,3,4...etc)

Set the amount of Memory for the VM to use:

**$ lxc config set win11 limits.memory=XGB   (X=2,4,8,16 etc)**

To verify it was configured to what you want

**$ lxc config show win11**

Start the win11 VM

**$ lxc start win11**

**Then get the VM’s IP address:**

**$ lxc ls win11**

**If it is an Incus VM:**

You can set the # of CPU and the amount of memory for the VM with the following commands:

**$ incus config set win11 limits.cpu=N**     (N=1,2,3,4...etc)

Set the amount of Memory for the VM to use:

**$ incus config set win11 limits.memory=XGB   (X=2,4,8,16 etc)**

 \
To verify it was configured to what you want

Start the win11 VM

**$ incus start win11**

 **\
NOTE 1:**

[Kimm Knight – Github repository for RemoteAppTool](https://github.com/kimmknight/remoteapptool?tab=readme-ov-file) \
[See RemoteAppTool Windows Compatibility Chart \
](https://github.com/kimmknight/remoteapptool/wiki/Windows-Compatibility)

**NOTE 2:**

These steps will call the Windows 11 VM - ***win11***.

The following steps ***work exactly the same*** whether you are using an LXD VM or Incus VM. \
 \

 \

**STEP 1 – Install RemoteAppTool on Windows**

Log into your Windows Desktop

If LXD:    \
**$ lxc console win11 -- type=vga** \
If Incus:  \
**$ incus console win11 --type=vga**

Start your Windows browser and goto Kimm Knight's Github page:

[https://github.com/kimmknight/remoteapptool?tab=readme-ov-file](https://github.com/kimmknight/remoteapptool?tab=readme-ov-file)

***​Scroll*** *to the* ***Download Section*** and **download the Installer** to your Windows VM.

My preference is to *copy the remoteapptool installer from \Downloads to my Windows Desktop*.

Execute the **remoteapptool** and you will see: \
 \
![](image1.png)

Clicking **+**  symbol to Add a RemoteApp brings up Windows Explorer.

Click on:  **Local Disk (C:)**

Click on   **Windows**

Scroll down to find:  **Explorer** 

Click on **Open**

![](image2.png)

 \


That will return you to the previous RemoteAppTool Menu where you will now see **Windows Explorer** listed.

![](image3.png)

**Double Click** on the **Windows Explorer** entry

![](image4.png)

Change the Name or Full Name here or just leave it.  Click **Save**

**STEP 2**

Using Windows Explorer find all applications you want to use on your Ubuntu Desktop.

![](image5.png)

 \
User installed Windows Apps are usually found in: 

**Program Files**  (see above)

Click on that and you will see a folder for every Windows App you have installed.

Go into each folder and find the “***executabl***e” for that app

 \


**Right Click** on that executable and a side menu opens:

![](image6.png)

Click on **Show more options**

The side menu changes, adding more options.

Click on **Create shortcut**

Windows will present another window telling you:

![](image7.png)

Click on **Yes**

Now ***repeat Step 2 for every Windows application you want to use*** on your Ubuntu Desktop.

**STEP 3**

***Enable*** Windows Remote Desktop

Click the **Windows Start Icon**

![](image8.png)

Finally, add one more Windows Shortcut to the Desktop.   

This *needs to be a WIndows Shortcut pointing to the Windows logoff executable:*      \
      \
               **C:\Windows\System32\logoff \**


Click on **Settings**

![](image9.png)

 **\**


Scroll down and Click on **Remote Desktop**

![](image10.png)

 **\**


Toggle Remote Desktop to **ON**

![](image11.png)

Click on **Remote Desktop users**

![](image12.png)

Click **Add** and enter **YourWindowsID** 

![](image13.png)

Click **Check Names**

Assuming Windows knows your Login ID then next just Click **OK**

*The configuration required for your Windows 11 VM* ***is now complete!***

**Sign-out/logoff Windows & Click X** to kill the windows **11 VM display.  \**
It will still be running but now “*detached*” & running in the Background.

**Step 4**

Add the following to your ***Ubuntu Home directory***.

In a **Terminal**...

Create a **Bash** file named **win11.sh** with this content:

**#!/bin/bash \
# 'Note: this script has some Long lines in it -- Edit it full screen'**

**#===={ Purpose }===================================================================**

**# This script will initiate an RDP Remote Desktop session with a 'target' Windows VM/server for the**

**# Current User.  It also** ***assumes that User has the same login ID on both Ubuntu & Windows*****!**

**# User will be prompted for Password.**

**#================================================================================ \
# Statically identify the target Windows IP address \
#================================================================================**

**# now use xfreerdp3 to log into the WIN11 VM**

**# IMPORTANT... \
# \
# ID the IP address of your Windows 11 VM (change the following X.X.X.X) to the IP address  \
# of your Windows VM**

**win11ip=X.X.X.X**

**#===============================================================================**

**# NAME:  win11.sh \
# \
# PURPOSE: \
# \
# Login to Windows and Execute a Remote App to display on  your \
# Linux Desktop using the Linux application called "xfreerdp", \
#===============================================================================**

**# Get User to enter a password for the win11 session \
# "NOTE" we hide display of password while entering it**

**clear \
echo \
read -s -p "Enter your Windows password: " win_pwd \
echo \
echo "Connecting to Win11 RemoteApps..."**

**# Start the Windows Explorer RemoteApp you created on Windows**

**LOG_LEVEL=OFF xfreerdp3 \ \
/cert:ignore \ \
/u:$USER \ \
/p:$win_pwd \ \
/printer \ \
/drive:auto,* \ \
/drive:shared-folder,/home/bmullan/shared-folder \ \
/app:program:'%windir%\explorer.exe' \ \
/v:$win11ip > /dev/null 2>&1**

**Explanation of “options” above:**

**/printer => will share all printers that Ubuntu knows with Windows 11**

**/drive:auto,* => will share ALL drives on your Ubuntu System with Windows 11**

**/drive:shared-folder,/home/bmullan/shared-folder => will share only a single folder on your \
     Ubuntu system. That folder has to pre-exist.   In this example:  I created a directory in my \
     /home/bmullan called “shared-folder”.**

     **NOTE:  you can configure 1 or both of the above “/drive” freerdp3 command options**

 **\
**continued... \
 \
/app:program:'%windir%\explorer.exe' \ \
    names the windows application that is your “remoteapp” \
 \
    NOTE:   the single “quotes” around => %windir%\explorer.exe**

Save the **win11.sh** file

Set the win11.sh to be executable:

**$ sudo chmod 777 ./win11.sh**

 **\**


**Install xfreerdp3 with both X11 & Wayland modules on Ubuntu**

**$ sudo apt install freerdp3-wayland freerdp3-x11 -y**

Next, **change to your Desktop directory.**

**$ cd ~/Desktop**

 \
***Create a launcher file*** called **win11.desktop** in your Desktop directory \
that you can click on to execute the win11.sh for you.   Use your favorite Text Editor.

Contents of => ~/Desktop/*win11.desktop*

#!/usr/bin/env xdg-open \
[Desktop Entry] \
Type=Application \
Name=Windows-11 \
GenericName=CIAB RemoteApps \
Comment=CIAB Windows 11 Remote App \
Type=Application \
Icon=<path & name of ICON you want to see for this on your Desktop> \
Categories=Utility; \
Keywords=remoteapp; \
StartupNotify=false \
Terminal=true \
Exec=/home/YourUserID/win11.sh

# Note: if you didn’t save the win11.sh in your ~/Desktop directory \
#           Change this Path to where it is located. \


Save the win11.desktop file and make it executable

**$ chmod +x ~\Desktop\win11.desktop**

 \
 \
 \
NOTE:   You can use any ICON you want in the above

To see what Icons ubuntu already has installed look here:

**$ cd  ~/.local/share/icons/hicolor**

But you can use any ICON of the appropriate size for a Desktop launcher

**That’s it...!** 

You may want to reboot your Windows 11 to make sure all the changes are in effect.

If everything was configured correctly you should be able to see your Desktop Launcher named:  **“CIAB Remoteapps”** 

Double click on it to execute it and you will get prompted for your Windows password.

If you configured your VM IP address correctly in the BASH script win11.sh you should see the Windows Explorer RemoteApp appear in its own Ubuntu Desktop Session Window.

Using that to navigate to your Windows “Desktop” and click on any of the Windows app “*shortcuts*” you made earlier and they to should appear, each in its own Ubuntu Desktop Session Window

**Addendum:**

**Xfreerdp has a lot of command options** that my script doesn’t use but you certainly can.

To see the full list of xfreerdp options execute:

***$ xfreerdp3  --help  | more***

Be aware that there is about 5-6 pages of command options that are documented.

**Tips:**

Some cmd options have a **default** *that does* **“auto-detect”** 

***Example*** (look in win11.sh)

For enabling File System sharing you can **share all Drives** on your Ubuntu system with Windows:

**/drive:auto,* \**

Or you can **share** just a **single specific directory**:

**/drive:shared-folder,/home/bmullan/shared-folder \**

The above shares a directory named “shared-folder” which I created \
in my Home directory**.**

If there are more command options you want to use say for **USB j**ust **append it to the end in the win11.sh** file.

Don’t forget the “**/**” and “**\**

***Great LXD or Incus Learning Resources***

Youtube channel for Scotti-BYTE Enterprise Consulting Services has many ***How-To videos*** for both LXD and Incus.

[https://www.youtube.com/@scottibyte/videos](https://www.youtube.com/@scottibyte/videos)

He also has an associated “***Discussion Forum***” on which he has posted \
*all of the Configuration steps for each respective video* allowing you to copy & paste when trying to do whatever it is about.

[https://discussion.scottibyte.com/](https://discussion.scottibyte.com/)

And look into Simos Xenitellis’s Blog:

[https://blog.simos.info/ ](https://blog.simos.info/) \

 \
If you are a Reddit user, there are also a LXD and Incus sub-reddits. \
Note that *neither* are ***Support forums use the links below*** for that.

These sub-reddits are meant to consolidate info from around the web on those two VM/Container systems.

 **\
For LXD:**

[https://www.reddit.com/r/LXD/](https://www.reddit.com/r/LXD/)

**For Incus:**

[https://www.reddit.com/r/incus/](https://www.reddit.com/r/incus/) **\**

**Reference Sites:**

**For freerdp/xfreerdp github:** \
[https://github.com/FreeRDP/FreeRDP ](https://github.com/FreeRDP/FreeRDP)

**For freerdp/xfreerdp discussion:**  [https://github.com/FreeRDP/FreeRDP/discussions](https://github.com/FreeRDP/FreeRDP/discussions) \**

**For LXD:** \
[https://canonical.com/lxd](https://discuss.linuxcontainers.org/)

**For LXD discussions/support (LXD Discourse):** \
[https://discourse.ubuntu.com/c/lxd/126](https://discourse.ubuntu.com/c/lxd/126) \**

**For Incus:** \
[https://linuxcontainers.org/incus/introduction/](https://linuxcontainers.org/incus/introduction/)

**For Incus discussions/support:** \
[https://discuss.linuxcontainers.org/](https://discuss.linuxcontainers.org/) \


 \


