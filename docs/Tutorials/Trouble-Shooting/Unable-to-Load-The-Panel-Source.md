## Here we go.

Sunday, September 15th, 2019.  Upon loading the home assistant UI, I receive an annoying javascript popup error:

`An embedded page on this page says`  
`Unable to load the panel source: /api/hassio/app/entrypoint.js.`

## Actions Taken

* Opened developer console in Chrome to investigate and found the following:  
    
    ![annoyingerror](/img/panelsourceerror.png)

* Found multiple references to custom-panel.ts.  Investigating further.
* SSH into HASS.io on Ubuntu server and ran `hassio ha check`.  Met with the following message:  
    ```Post http://hassio/homeassistant/check: dial tcp 172.30.32.2:80: connect: no route to host```

    Okay, so we have a potential network issue between docker and Ubuntu.

* Attempted `hassio ha restart` only to see the same `no route` message.  Not sure what it means.
* SSH into Ubuntu and check docker: `systemctl status docker`.  Incidentally, I can't stop typing cocker by mistake.  Result is a list of running docker containers.
* `sudo apt-get update && sudo apt-get upgrade`
* Checked docker status, none of the containers appear to be running.  Still have limited experience and understanding of Docker.
* Rebooted Ubuntu server.
* After some time, Home Assistant came back online, but with a few problems, including some Ingress errors, likely due to a change in port numbers on containers and a firewall that's blocking those new ports.
* Added both offending ports to FirewallD.
* Add-ons that weren't loading due to 502 Bad Gateway are now loading without fail.

It appears the problem has been solved.

## No Idea What Caused It
