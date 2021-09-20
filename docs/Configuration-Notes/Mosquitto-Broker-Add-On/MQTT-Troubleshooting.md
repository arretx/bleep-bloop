## 08-29-2019

At this point in the process, there is no MQTT integration setup and no MQTT devices defined in configuration.yaml.  Unknown if there are lingering remnants of past configured devices or not.  Don't know where to find that.

### Steps Followed

* Per [this page](https://www.home-assistant.io/addons/mosquitto/#using-mosquitto-with-hassio):

    1. ☑️ Install official Mosquitto add-on from the Add-on store.
    1. ☑️ Make sure that Start on Boot is active and Protection mode is by default active.  Not sure why this is there or what I would need it for.
    1. ☑️ Starting add-on with default configuration settings.
    1. ☑️ Everything appears to be okay with the start-up.  All ports are listening, my user name was found on Home Assistant, and 6 tasmota devices configured to access MQTT connected in the Mosquitto logs, reporting each their IP and using my HA user name to connect.
    1. ☑️ Tested external MQTT client (MQTTfx) and it connected and could scan all available topics.  MQTT log reflects client connection from MQTTfx.
    1. ☑️ Visited Configuration --> Integrations and found a newly discovered MQTT service with a configure button.  This is a good sign.
    1. ☑️ Clicked the `CONFIGURE` button on the Discovered integrations page and then `Enabled Discovery`, then `Submit` and `Finish`.

At this point, I see MQTT: Mosquitto Broker listed in the Integrations as a Configured integrations, with zero devices.

#### Errors in Hass.io system log:

Immediately there are errors in the Hassio system log.

```
19-08-29 19:15:08 ERROR (MainThread) [asyncio] Fatal error on transport
protocol: <uvloop.loop.SSLProtocol object at 0x7f70782161d0>
transport: <TCPTransport closed=False reading=False 0x556e364f7320>
Traceback (most recent call last):
  File "uvloop/sslproto.pyx", line 574, in uvloop.loop.SSLProtocol._do_shutdown
  File "/usr/local/lib/python3.7/ssl.py", line 778, in unwrap
    return self._sslobj.shutdown()
ssl.SSLError: [SSL: KRB5_S_INIT] application data after close notify (_ssl.c:2629)
19-08-29 19:15:08 INFO (MainThread) [hassio.discovery] Discovery c6e8d9f244124e72a70a4fc757820561 message send
19-08-29 19:15:11 INFO (MainThread) [hassio.auth] Auth request from core_mosquitto for jgriffith
19-08-29 19:15:11 ERROR (MainThread) [asyncio] Fatal error on transport
protocol: <uvloop.loop.SSLProtocol object at 0x7f70782161d0>
transport: <TCPTransport closed=False reading=False 0x556e368e49e0>
Traceback (most recent call last):
  File "uvloop/sslproto.pyx", line 574, in uvloop.loop.SSLProtocol._do_shutdown
  File "/usr/local/lib/python3.7/ssl.py", line 778, in unwrap
    return self._sslobj.shutdown()
ssl.SSLError: [SSL: KRB5_S_INIT] application data after close notify (_ssl.c:2629)
19-08-29 19:15:11 INFO (MainThread) [hassio.auth] Success login from jgriffith
19-08-29 19:17:11 INFO (MainThread) [hassio.auth] Auth request from core_mosquitto for mqtt
19-08-29 19:17:11 ERROR (MainThread) [asyncio] Fatal error on transport
protocol: <uvloop.loop.SSLProtocol object at 0x7f70782161d0>
transport: <TCPTransport closed=False reading=False 0x556e368e49e0>
Traceback (most recent call last):
  File "uvloop/sslproto.pyx", line 574, in uvloop.loop.SSLProtocol._do_shutdown
  File "/usr/local/lib/python3.7/ssl.py", line 778, in unwrap
    return self._sslobj.shutdown()
ssl.SSLError: [SSL: KRB5_S_INIT] application data after close notify (_ssl.c:2629)
19-08-29 19:17:11 WARNING (MainThread) [hassio.auth] Wrong login from mqtt
19-08-29 19:17:11 ERROR (MainThread) [asyncio] Fatal error on transport
protocol: <uvloop.loop.SSLProtocol object at 0x7f70782161d0>
transport: <TCPTransport closed=False reading=False 0x556e368e49e0>
Traceback (most recent call last):
  File "uvloop/sslproto.pyx", line 574, in uvloop.loop.SSLProtocol._do_shutdown
  File "/usr/local/lib/python3.7/ssl.py", line 778, in unwrap
    return self._sslobj.shutdown()
ssl.SSLError: [SSL: KRB5_S_INIT] application data after close notify (_ssl.c:2629)
```

Lots of references to SSL.  Not sure what this is all about and no idea how to fix it.

### Potential SSL Problem

I have a public facing NS that's running Apache2 and Virtualmin so I can manage virtual websites for a few people, tied to their domain names.  My domain name is one of those virtual servers, and hassio.mydomainname.com is the sub-domain I use to access my instance of Hass.io, which is running in Docker on the same server.

I used Let's Encrypt to establish the ssl.cert and ssl.key files for hassio.mydomainname.com.  I'm not a web admin expert yet.  So, in order to setup hassio so it would load as a secure site, I tinkered around with Apache, following the guide for Apache Proxy on the HASS.io documentation site.

I also copied the contents of ssl.cert and ssl.key and inserted them into the files /ssl/fullchain.pem and /ssl/privkey.pem.  It wasn't until I did this that the browser would finally allow SSL connection from outside of my network.

I have a hunch this isn't the right way to do it, or I'm missing something completely.

Since the above errors contain reference to SSL, I checked the contents of the Apache ssl files against the *.pem files in HASS.io and I found what I suspected would be the case.  Every 2 months my server automatically updates the ssl.cert and ssl.key files on the server for Apache, but the pem files in Hass.io are not updated.  (Again, this doesn't feel like the way it's supposed to be setup.)

So, currently the contents of the key files match, as I've updated the hass.io versions to match.  I wonder if this will remedy the above errors.

1. ☑️ Checked config from Configuration --> Server Control
1. ☑️ Turned off Logger - cause it pisses me off.
1. ☑️ Restarted Home Assistant.
1. ☑️ No detected MQTT devices.
1. ☑️ Logged into one of my Tasmota devices (Wemos D1 Mini) and on a hunch, changed the `Client` setting in Configuration --> Configure MQTT.  After saving, the device re-booted and appeared in the integration for MQTT.
1. ☑️ Repeated the process for another device and it worked also.

## Partial Conclusion

It's not clear whether or not there were a combination of changes that I made that led to auto discovery, but I'm suspecting that it was the `Client` name setting in the device itself that needed to appear as a new device to HA.  

That brings into question the location of the previous information.

### QUESTION

If changing the client from the default of DVES_%06X to say, the name of the switch, like kitchen lamp, what is actually happening in auto-discovery that's different from just leaving it as is?