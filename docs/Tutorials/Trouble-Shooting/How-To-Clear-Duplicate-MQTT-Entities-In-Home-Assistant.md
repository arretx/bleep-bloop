You may find yourself looking at duplicate entities in your list of devices after using discovery to integrate MQTT devices into Home Assistant.

I have had this problem, and I now know how to fix it.

### Steps to Fix the Problem

!!! Disclaimer
    My process is dependent upon [my basic configuration](http://127.0.0.1:8000/Configuration-Notes/My-Basic-Configuration/) and may not be reflective of what you need to do to fix your duplicate entries.

1. Stop the Mosquitto Add-on, or stop the Docker container for core_mosquitto through Portainer or through your CLI.
2. Stop Home Assistant. Login to HASS.io via SSH and type the command: `hassio ha stop`.
3. Login to your server via SSH and navigate to the `/data/core_mosquitto` folder wherever your HASS.io add-ons are installed.  In my case it was `/usr/share/hassio/addons/data/core_mosquitto`. Incidentally, I had to create a symlink to store the entire contents of HASS.io on a larger drive, since my server's main drive wasn't big enough to handle some of the storage demands of a few of the add-ons I use.
4. If you don't feel comfortable deleting the mosquitto.db file, simply move it to another file name: `sudo mv mosquitto.old` or something of that nature.
5. You'll need to open the `core.restore_state` file that's stored in the hidden `.storage` folder in the root of your Home Assistant folder.  In my case, that location is `/usr/share/hassio/homeassistant/.storage`.  Make sure you have read/write privileges so you can edit this file.
6. In the core.restore_state file, find the offending entries and delete them.  Save the file.
7. In your HASS.io SSH terminal (remember step 2,) re-start Home Assistant: `hassio ha start`.
8. Once Home Assistant is up and running, make sure Mosquitto broker is up and running.  If you stopped it using docker in the command terminal, just start it up again.  If you have the Portainer add-on, you can use it to start the container.
9. In my instance, all of my devices appeared in the Mosquitto Integration with (entity unavailable) next to them.  To remedy this, open the console for each device by logging into its web interface and re-activate `SetOption19 1`.  You should immediately see your device come back to life in the Mosquitto Broker integration screen.
10. Have a beer.  You're done.