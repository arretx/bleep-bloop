## That Dreaded 502 Bad Gateway Error

Okay.  So this one drove me nuts for a long time, and when it came down to it, the fact that I couldn't solve the problem was due to the simple fact that I was missing a pretty obvious clue.

So, as of today, in my setup, I believe that I have solved the problem, but we'll see what happens in the future.

## The Problem

After a fresh install of HASS.io on Ubuntu 18.04 in Docker, I was met with problems using add-ons that utilize Home Assistant's ingress feature.  
!!! Preface
    If you aren't familiar with ingress (and I really don't 100% understand it either), it allows us to bypass the need for individual credentials for each add-on we install, and instead uses the core user credentials in Home Assistant to authenticate, open, and login to the add-on inside of the HASS.io user interface.

    Many add-ons are simply third party apps that are already out there that require their own username and password to access.  Add-ons like NodeRED, Portainer, ESPHome, etc.

    With ingress, we should be able to simply open the WEB UI of each addon without needing to setup those credentials for each add-on.

## 502 Bad Gateway - NodeRED Primarily - But Others Too

A few of the add-ons that I installed in HASS.io failed to open.  In fact, they would simply display a 502 Bad Gateway error.  Naturally, this sounds like a networking error, but I was unsure why it was happening, and I didn't think at all to research firewall settings because I read somewhere that there shouldn't be any need for firewall adjustments due to how Docker networks work with Linux firewalls.

Alas, I was wrong.  So, for a few months I've been pulling out my hair trying to figure out what is causing this.  It wasn't until I spun up an entirely new Ubuntu box, installed HASS.io and NodeRED, and ran into the SAME problem that I concluded that it could be only one thing.

My Router.

Again I was wrong.  The router is fine.  It turns out that the firewall on my server(s), which is FirewallD, doesn't play nice with Docker.  Now, at the root of it all, I still don't quite know what that means, or how to diagnose or fix that problem.  But, I did figure out how to make the 502 Bad Gateway Error go away.

## Steps to Solve

!!! Note
    Let it be known that I also use Webmin as my primary management interface for my Linux server, which makes it a bit easier to slide into the world of CLI management, but makes it a challenge to publish "how to" examples because I don't edit files directly much.

Assuming you're using HASS.io in Ubuntu with FirewallD _and_ Docker, here's how I fixed the problem.

1. In HASS.io, try to access the offending add-on to cause the 502 Bad Gateway error.
* From the HASS.io page in Home Assistant, click the System Tab.
* Make a note of the most recent error, which should be written in a `red` font color.
* In that error, you'll see an IP address _and_ a port number.  

    (This is where the embarrassing part is...since I _did_ originally think it was a network problem, but completely overlooked the port number that was being accessed.  Furthermore, I have an identical setup at my Church that _didn't_ experience this problem, and it turns out the reason it didn't was because the port used was already open on the firewall.)

* In FirewallD, open that port.
* Apply your firewall settings.
* Restart HASS.io for good measure.  I don't know if you really need to, but hey, I did.
* Access your WEB UI from that add-on that was throwing the 502 Bad Gateway error.

Did it work?  Let me know.