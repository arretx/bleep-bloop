## What You'll Need

* Home Assistant with the ESPHome Add-On installed.
* A Wemos D1 Mini micro controller board.
* A WIFI Network (Obviously)
* The right kind of USB cable.  Not just the right connectors, but the cable must have at minimum 4 conductors with 4 connected wires.  There are a lot of Micro-USB cables out there that are wired up with **ONLY** power and ground for charging.  You must have the other two conductors for data transfer, otherwise you won't be able to flash the board.
* The latest [ESPHome Flasher](https://github.com/esphome/esphome-flasher/releases)

## Steps

* Connect your Wemos D1 Mini to your computer via USB (note, I'm using a Mac for this)
* Open Home Assistant and load up the ESPHome Add-On.
* Create a new node in the add-on.  Fill out the form with the information that matches.
* Flash the board and look at the logs to confirm that it's connected to the network.
* (Optional) Login to your router and assign a dedicated IP address to this device so it's always the same.  Use DHCP to match the MAC Address to the IP Address.  No need to set the static IP at the device level if you do this.

You can now disconnect the board from your computer and connect it to a 5V power source and it will connect to your WIFI network.

Test it.  Connect it to 5V power and see if you can see the logs in ESPHome.

Now, go to ESPHome's website and figure out what you want to _do_ with the D1 Mini.  This is where you'll get the syntax to enable the various sensors / relays, etc., that you might have.

Once you enter the configuration information, upload it to your device using the ESPHome add-on.

After that's completed, go to the Home Assistant integrations page and you should see a newly discovered device, which should match what you've actually connected to it.  

Now, go have fun using the sensors to automate whatever you want.