---
layout: blank
title: "Installing RCT on Mac"
permalink: rctmac
---

# Installing GoG's version of Roller Coaster Tycoon Deluxe on Mac OS with Wine

Before doing all this, make sure you've downloaded the game from GoG.

### Wineskin

Wineskin is a wrapper around WINE, a great emulator (technically compatibility layer, but no one cares) for Windows applications for Mac and Linux. Wineskin makes it all easy to use on a Mac, instead of having to putz around with the command line. You want to download the Wineskin Winery, which creates Mac application packages around your emulated apps. You can download it [here](http://sourceforge.net/projects/wineskin/files/Wineskin%20Winery.app.zip/download). Drag the unzipped dmg into your Applications folder.

### Setting up Wineskin

When you run Wineskin for the first time, hit "Update" under "wrapper version" to install the newest version of the wrapper. It should look something like this after:

![](http://i.imgur.com/w4gpU.png)
Click the "+" icon under "Installed engines".

![](http://i.imgur.com/5NWed.png)
Select whatever the topmost option is under "available engines", then click "Download and Install".

![](http://i.imgur.com/3pIg4.png)
Click OK and wait for the install to finish.

### Creating the wrapper

![](http://i.imgur.com/uVc95.png)
Click "Create New Blank Wrapper". Name the wrapper whatever you want ("RCT", for example).

![](http://i.imgur.com/Fado8.png)
When this comes up, click "Cancel". You don't need it for anything in RCT.

![](http://i.imgur.com/f553E.png)
Click "View wrapper in Finder". Open the new RCT application.

### Installing RCT

![](http://i.imgur.com/vyXgA.png)
Click "Install Windows software" and locate the RCT install file you downloaded from GoG.

![](http://i.imgur.com/3DGVN.png)
Here, select "Options" before beginning the install.

![](http://i.imgur.com/JZxde.png)
Uncheck Foxit (a Windows PDF reader) and desktop/start menu icons (which Wine will try to make for you, but they won't work). Then start the install.

![](http://i.imgur.com/OQBPq.png)
Once it's done, click "Exit Installer".

![](http://i.imgur.com/t1GK0.png)
Hit OK, but we're about to change this in a second.

![](http://i.imgur.com/vyXgA.png)
Click "Advanced".

![](http://i.imgur.com/qQ967.png)
Click "Browse" and navigate to "Program Files\GOGcom\RollerCoaster Tycoon Deluxe\" and select "RCT.exe", then click "Done" and "Quit".

### Configuring RCT

When you first launch RCT, it'll come up in fullscreen, and likely not allow you to start a scenario (the first option on the main menu). To set it to windowed mode, select the 3rd icon (Tutorial), take control of the mouse, and select the Floppy disk menu in the top-left and click "Options". Under display mode, click "Window", then restart RCT. When you come back to it, it should come up in a normal window that can be moved and resized.