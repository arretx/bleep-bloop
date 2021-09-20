## Variables in NodeRED

In NodeRED, you can create arbitrary variables that store information based upon the messages that flow through the nodes.  You can store information in variables that are accessible by a single **node**, an entire **flow**, or **globally**, meaning the whole of NodeRED.

!!! note
    A few things are assumed before you continue.  1. You operate in the HASS.io environment.  2. You have installed the NodeRED add-on for HASS.io and you can get to it.  Beyond that, there may be a few nodes that you'll need to install from within NodeRED.  Otherwise, you're ready to get started.

## **Exercise**

Do these steps and be amazed in wonderment.

### Create an Object containing _all_ of Home Assistant's States

1. Open NodeRED.
1. Create a new Flow, just to keep things clean.  Name it "**Set Variables**."  Or whatever you want.  Just remember what you call it.
1. Drop an `Inject` node into the flow.  If you don't know how to do this, use the search field in the upper left corner of NodeRED to search for "Inject" and then drag the resulting node to the flow.  Inject nodes can be triggered based upon interval timing, or by pressing the little button to the left of the inject node.  They're a perfect tool to use to fire off messages to test your flows.  The injected message is a unix timestamp (the number of seconds that have passed since January 01, 1970.)
1. Next, throw a Function node in the mix.  You're about to write some very simple code.  Attach the `out` of the inject to the `in` of the function node.
1. Double-click the function node and name it something like "**Global States / Services**."
1. In the Function area, add the following code:  
   ```
   global.set("states", global.get('homeassistant').homeAssistant.states);
   global.set("services", global.get('homeassistant').homeAssistant.states);
   ```  
   This basically says, "Hey you, NodeRED, store all of Home Assistant's states in a global variable called `states`, and store all of Home Assistant's services in a global variable called `services`."
1. Click `Done` (upper right corner of the function editor).
1. Click Deploy.
1. The inject node has a small button on the left side.  Click that button.  You have just stored