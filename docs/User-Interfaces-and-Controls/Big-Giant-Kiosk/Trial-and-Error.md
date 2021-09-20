#Trial and Error

!!! hint
    Any time you see "Trial and Error" you'll be presented with the initial setup process that I went through prior to success or complete abandonment of the project.

## Steps

The following are the steps I took to begin creating the Big Giant Kiosk.  My intent is to piece together as much of what's already been accomplished already to get to a usable tool in the house.

1. Researched Chromium OS for the Raspberry Pi3 after reading bout others who have done this type of project.  I haven't done it before, but it seems that running a browser on the Pi3 that can open in kiosk mode and access Home Assistant would be the easiest way to deliver a video signal via HDMI to any display input, thereby removing the size limit of the output device.

2. So I guess the first step is to download [Chromium OS](https://sourceforge.net/projects/chromnium-os-for-all-sbc/files/Raspberry%20Pi%20Images/Raspberry%20Pi%203/) from [SourceForge](https://sourceforge.net).  At the time of this article, the file I downloaded was called "SamKinison_v0.5_pi3_16GB.tar.xz" which extracts to "SamKinison_v0.5_pi3_16GB.img."

3. Since I didn't already have Etcher installed on my MacBook Pro, I had to [download that](https://www.balena.io/etcher/) and install.

4. Next, I threw a 32GB USB stick in my MacBook and made sure I could identify it.  Etcher auto-selected the USB stick and was correct about which one it selected...so that was easy.  After clicking "Flash" I had to enter my MacBook Pro User Credentials to proceed, and off we go...it seems at this point that it's going to take about 25 minutes...so I'm heading out to get some beer.

5. Mmm...beer.

6. After writing the image to the USB stick, Etcher automatically dismounts the drive so you won't see it in your Finder.  That's okay.  Just remove it from your system and stick the SD card into the Raspberry Pi3's memory slot.  Connect a keybaord, mouse, and monitor, and power it up with a 5V power supply.

7. What a nice surprise to see the Chromium OS boot up almost instantaneously.  

8. It was at this point that I realized I didn't have my keyboard and mouse, so I had to put the rest of this off until the following day...which is now.

9. After connecting the mouse to the 2nd USB port, I wasn't getting a connection confirmation.  The keyboard recognized immediately.  Strangely, the mouse didn't work in any of the other 3 ports.  So, I tried the keyboard in one of those ports.

** In the middle of this process, the Chromium screen went blank with a gray background and no response from anything.  Not sure what was happening at this point **

10. So, I unplugged the Pi3 and plugged it back in again.  This time, the system loaded and prompted me only for the mouse connection.  I tested all of the USB ports with the keyboard and each port connected without fail.  Something must be wrong with this mouse.

** Or it could simply be that I wasn't plugging the mouse in at all and instead was plugging in my FTDI USB adapter without realizing it. **

11. After clicking continue, since I could now, I selected the appropriate options for Language and Keyboard, and Network.

** This is where I realized that WIFI wasn't turn on, as there was no WIFI network detected and it read "WI-FI is turned off."