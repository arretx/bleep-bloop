# How to Setup the Monoprice 6-Zone Amplifier to work with Home Assistant

The [Monoprice 6-Zone Amplifier](https://www.monoprice.com/product?p_id=10761) can be remotely controlled by [Home Assistant](https://home-assistant.io).  It does NOT support connecting directly to your network, nor WIFI.  The remote control is achieved through a 9-Pin RS-232 serial interface.

## What you'll need

  1. (Hardware) Monoprice 6-Zone Whole House Audio Amplifier
  1. (Hardware) Audio Sources (Walkman, Discman, iPod, Dante Avio Adapter...basically anything that you can convert to RCA or 3.5mm.)
  1. (Hardware) Speakers for amplified signals, or a mixer to receive line level output (great for supplementing a zone with a sub-woofer)
  1. (Hardware) A way to communicate with the Amplifier through RS-232.
    1. This means you either need a USB to RS232 cable and a direct connection to your Home Assistant host, or...
    1. ...an [IP to RS-232 Network Converter](https://www.amazon.com/USR-TCP232-306-Serial-Ethernet-Device-Server/dp/B07G5P4CPR) so you can connect directly to your network. (This is how I did it.)
  1. (Software) Home Assistant running on your network.


## Hardware Specific to My Setup

  1. Monoprice 6-Zone Amp
  1. An officially supported version of Home Assistant running as a Virtual Machine on your preferred system.  I'm running mine as a VM on my Unraid system.
  1. A network switch, managed (a managed switch is not required but a switch is definitely required, of course if you're doing this and you don't know that, you have some homework to do about networking).  I have Ubiquiti Unifi ecosystem with UDMPro and Protect security cameras...etc.
  1. A [USR-TCP232-306](https://www.amazon.com/USR-TCP232-306-Serial-Ethernet-Device-Server/dp/B07G5P4CPR) IP to RS232 Network converter.
  1. A [Male to Female DB9 serial cable](https://www.amazon.com/dp/B009WUQ828?psc=1&ref=ppx_yo2_dt_b_product_details).  MALE TO FEMALE _if you purchase this particular converter_.  There are other models that have a Female RS232 Connector which would require a male to male cable instead.

## Configuration Instructions

### RS232 IP Converter

The firmware version on the IP232 converter when I wrote this was V4018.  The [usriot](https://www.usriot.com) website has all of the tools you need to configure and test this device, but they're not easy to understand if you don't know anything about them, like me.

This particular converter came pre-configured with a 192.168.0.0/24 ip address.  Mine was 192.168.0.7.  If you don't have the luxury of creating multiple networks, you may need to adjust your computer to be on the same network temporarily just so you can access the web interface of the device.  In my case, I was able to create another network for this device and was able to access the web interface even though my laptop is on the 192.168.1.0/24 network.

!!! Note
    With the exception of the web interface embedded on the device, all of the software tools designed to help you with this device are windows based.  If you don't have Windows, you could run a VM to use the tools, but you'll run into some challenges trying to detect the device.  The VM needs to be on the same network as the device.  I used VMWare on my Mac to run Windows and set the network settings to "Autodetect" under bridged settings.  It wasn't until I did this that I could use the USRIOT test tools to, at bare minimum, confirm that I could connect to the device.



#### Preparing the Static IP Assignment

  1. **Make a note** of the **MAC Address** on the label on the underside of your IP to RS232 converter.
  1. **Login** to your router's **management console**.
  1. In your Router, **find** wherever you can set the static IP of a device and **add this device's MAC address** with a unique IP address of your choosing on your correct network so the device always has the same address, and it's always assigned via DHCP statically.
  1. **Make a note** of this IP Address.  You'll need it in the next section.
  1. **Save** your router settings.

#### Configuring the IP to RS232 Converter

  1. You may need to adjust your computer's IP address manually to be able to access the IP converter.  On the **underside of the converter**, there is an IP address.
  1. **Open** this address in a web browser.  Default credentials: USR - admin, PWD - admin
  1. On the left menu, **click** "Local IP Config."
  1. **Change the IP Type dropdown to DHCP**.  Leave all other settings alone on this page.
  1. **Save** the settings and **reboot** the converter (menu on the left).
  1. To **reconnect to the device**, enter the IP address you assigned to the MAC Address from the previous section, step 3.  Remember, you wrote it down.
  1. Once you get back into the configuration site for the device, **click** the **Serial Port** item on the menu.
  1. For baud rate, **enter 9600**.  Data size = 8. Parity = None. Stop bits = 1.
  1. Set the Work Mode to TCP Server.  That was the magic sauce that I was missing and didn't realize I had done before.
  1. The local port number should read 8235.  It can be whatever you want it to be, so go ham on it if you choose.  This is the port that you'll use when you setup the integration in Home Assistant.  I learned this the hard way after using port 80, like a moron.  To be fair, the first time I tried this Local Port Number, it didn't work.
  1. **Click** Save

There are no other settings I needed to configure in the converter to get it to work.

### Home Assistant Configuration

At the time of this revision: 

**Core Version - Core 2022.5.1**  

  1. **Login** to your Home Assistant UI.
  1. **Navigate** to the **Configuration** page on the left menu.
  1. **Click Integrations**
  1. On the Integrations page, **click** the **Add Integration** button in the bottom right corner.
  1. **Search** for Monoprice.
  1. **Click** on Monoprice in the resulting list.
  1. **Enter the following** in the **Port** field:  `socket://<ip_address>:<port>` where the ip adress is the address you assigned to your converter and the port is the port number you saved on the **Serial Port** page in the converter's web interface.
  1. If you want to give your six zones names right now, you can, otherwise, just **click submit**.

That's it.  If everything is connected correctly, you should experience a successful configuration.  Now, when you look at your media players in your **Developer Tools --> States** tab, you should see 18 different media players (zones) where 11 - 16 are 1 through 6 and the rest are to accommodate expanding your system to more amplifiers.

## Behavior Notes

### Keypads

The keypads that come with the amplifier are basically unnecessary if you're using HA to control the device.  However, if they are connected, they still should work alongside the RS-232 input.  One does not disable the other.

### Media Player Entities

It is my experience that there is only a single status and only 7 attributes for each zone "media player."  If you're playing a song in spotify, for example, and you have that setup as a media player in Home Assistant, you'll see in your Spotify attributes everything you want to know about what's playing, but in the //media player for the zone//, only the volume, power, and mute will be visible.

  * Entity State is either `on/off`
  * Attributes to expect:
    * source_list | These are named when you configure the integration.  If you don't name them, no sources will be available for each of the 6 media players (zones).
    * volume_level | floating point
    * is_volume_muted | boolean
    * media_title | So far, this only shows the name of the Source chosen for the zone.
    * source | The source chosen for the media player.
    * friendly_name | string
    * supported_features | [This Thread](https://community.home-assistant.io/t/how-to-read-interpret-supported-features/114152/15)


### What can you control?

From any given individual media player zone, you can adjust the power, volume, and mute.  There is no control over bass/treble as of this posting.

### Communication Delay

It seems that when you adjust one of the entities in Home Assistant, the device responds nearly immediately, but when you adjust the device using the keypads or IR remote, it takes a few seconds for the state information to make it back to Home Assistant.  Small price to pay, and not a big deal if you choose to _not_ install the keypads.