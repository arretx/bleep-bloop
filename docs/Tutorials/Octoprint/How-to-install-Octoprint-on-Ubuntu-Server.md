### Introduction
Octoprint is a web based management tool to control your 3D printer.  It runs on various operating systems and hardware configurations, but for the purposes of this document, we will be focusing on Ubuntu Server 18.04.

!!! note
    A big shout out to [Chris Riley](https://www.youtube.com/channel/UCqRiv7rQuxge63bqJ2hVNUQ) for his tutorial on YouTube.  I often re-publish instructions like this to my own site so I have a quick reference, and I re-write them _as I actually perform the installation_ so I can confirm that it works.

### My Hardware Configuration

* **Computer System** - Gigabyte mini PC with 4GB Ram and a Core i3-4010U at 1.7GHz.
    * This system's primary function is to run Home Assistant, but since Home Assistant is running as HASS.io in a Docker container, I'm able to still utilize the entire system as a full Ubuntu Server.

* **3D Printer** - For this system, we have connected the Ender3.  My other Ubuntu Server (16.04) which acts as my name server for web-hosting, etc., is connected to the Creality CR10S-Pro with a separate instance of Octoprint.

* **Connections** - All of my systems are connected via hard wired Ethernet connections and the active networking equipment that I use to manage my network is all Ubiquiti Unifi gear.

## Getting Started

!!! Note
    It is assumed that you already have Ubuntu up and running, as that is not covered here, and that you already know how to SSH into your system and run basic command line interface (CLI) commands.  We will also not cover configuring Octoprint for your particular printer.  This is solely a guide to help you get Octoprint installed and responsive.

### Steps to Install

All of this is done through your server's command line interface (CLI).  

1. `sudo apt update`
1. `sudo apt ugrade`
1. `sudo reboot now`
~~~
sudo apt update
sudo apt ugrade
sudo reboot now
~~~
After your server reboots, login via SSH again and continue.
~~~
sudo apt install python-pip python-dev python-setuptools python-virtualenv git libyaml-dev build-essential
cd /opt
mkdir Octoprint && cd Octoprint
virtualenv venv
source venv/bin/activate
pip install pip --upgrade
pip install https://get.octoprint.org/latest
sudo usermod -a -G tty <username>
sudo usermod -a -G dialout <username>
~/OctoPrint/venv/bin/octoprint serve
~~~
Test to see if you can get to OctoPrint by browsing to https://<serverip>:5000

You may need to allow port 5000 in your firewall.  For example `sudo ufw allow 5000`.  This will be system dependent.

After enabling your port, make sure you reload your firewall rules or restart your firewall.

The first thing you'll see when you hit the OctoPrint web page is a Setup Wizard.  Again we are not covering any details regarding OctoPrint setup here.

### Steps to Autostart Octoprint Service

~~~
wget https://github.com/foosel/OctoPrint/raw/master/scripts/octoprint.init && sudo mv octoprint.init /etc/init.d/octoprint

wget https://github.com/foosel/OctoPrint/raw/master/scripts/octoprint.default && sudo mv octoprint.default /etc/default/octoprint

sudo chmod +x /etc/init.d/octoprint
~~~

Edit the octoprint configuration file

`sudo nano /etc/default/octoprint`

Uncomment some of the lines and substitute your user name where you see <username>

~~~
# Configuration for /etc/init.d/octoprint

# The init.d script will only run if this variable non-empty.
OCTOPRINT_USER=<username>

# base directory to use
BASEDIR=/home/<username>/.octoprint

# configuration file to use
CONFIGFILE=/home/<username>/.octoprint/config.yaml

# On what port to run daemon, default is 5000
PORT=5000

# Path to the OctoPrint executable, you need to set this to match your installation!
DAEMON=/home/<username>/OctoPrint/venv/bin/octoprint

# What arguments to pass to octoprint, usually no need to touch this
DAEMON_ARGS="--port=$PORT"

# Umask of files octoprint generates, Change this to 000 if running octoprint as its own, separate user
UMASK=022

# Process priority, 0 here will result in a priority 20 process.
# -2 ensures Octoprint has a slight priority over user processes.
NICELEVEL=-2

# Should we run at startup?
START=yes
~~~

Add default to autostart:
`sudo update-rc.d octoprint defaults`

Check octoprint status:
`sudo service octoprint status`

Add users to sudoers file so it can run shutdown comands:
`sudo nano /etc/sudoers.d/octoprint-shutdown`

~~~
cd /etc/systemd/system
sudo nano octoprint.service

