---
date: 2023-01-28
---

# How I screwed up my Monoprice 6-Channel Amp Integration
!!! Note
    **May 6th, 2022**

    I managed to solve the problem, and I have updated [the original article](http://localhost:8000/Tutorials/Whole-House-Audio/Setting-Up-Monoprice-6-Zone-Amplifier/), so you can ignore this, or use it as a learning tool in case you're dealing with the same problem.

!!! Note
    **May 5th, 2022**
    
    The original article can be found [here](http://localhost:8000/Tutorials/Whole-House-Audio/Setting-Up-Monoprice-6-Zone-Amplifier/), which worked at the time I wrote it.  Please read this article first.



The concept is simple.  Convert Ethernet to Serial data and back.  A true bi-directional setup that allows you to send serial commands to the Monoprice 6-Channel Amp.

Here's what happened.

Last year I purchased the amp and the [USR-TCP232-306](https://www.amazon.com/USR-TCP232-306-Serial-Ethernet-Device-Server/dp/B07G5P4CPR) Ethernet to Serial converter.  I connected everything, configured the converter through the web-app, and it worked.  Since I confirmed it worked, and I knew the actual install was months away, I set it aside.

Here I am on May 5th, 2022 and I'm fighting with this stupid little converter.

My network has grown over the past year.  All of my little devices are on their own VLAN, so I wanted this device to also be on the same network.  So, I attempted to reconfigure the IP address on the device.  This is where everything went to shit.  My integration connection broke in HA and I wasn't able to get it back up and running.  I was able to reserve the correct IP addres on the network so I could access the converter, but once I was in, and all of the settings were correct to the best of my knowledge, I was unable to re-connect the integration through HA.

I ran a port scan of the device and the only ports that are open are 80 and 1501.  I can't figure any way to force the ports that are assigned in the Serial Port tab to be exposed to the network, so HA cannot connect to it using the correct port.

This is a problem because although using port 80 or port 1501 appears to complete the integration setup, these ports do not communicate over the RS232 connector.  Worse yet is that simply because the integration thinks it was successful, HA automatically sets up all of the required media_players.  It's not actually relying on the existence of a confirmed connection to the amp to register the new media_players, and thus, none of them actually connect to anything.

That's why they all say "unknown" next to them.

I've now been working at this for about 10 hours, and I'm convinced there's something that I've done to screw it up, but I can't figure out what it is.

If there's anyone out there who has figured this thing out, or has used a tried and true IP to RS232 converter that works, please fill me in.  Thanks.