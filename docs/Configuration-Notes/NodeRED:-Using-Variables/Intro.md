# How I Use Variables in NodeRED to Make Home Assistant Do What I Want

In NodeRED, you can store information that's already flowing through the nodes in variables that can then be used by other flows to accomplish your automations.

The context of your flow will help you determine where to store that information.  You can store variables:

  * In a Node, which is accessible only within that node.
  * In a flow, which is accessible by all nodes within that flow.
  * Globally, which makes the information available anywhere in NodeRED.

For simplicity sake, I typically just store my variables Globally.

## Function Node

One way to store variables is by using a function node.  Here's a real world example in my setup.

I have a coffee maker which is connected to a SonOFF Basic switch.  When that switch is turned on, a timer starts.  When that timer runs out, a random coffee commercial is played on a media player of my choosing.  In order to play that sound, I need to point NodeRED to the correct folder containing those coffee commercials.  To do this, I need to define a path and store it as a Global Variable.

All of the audio files that I use in Home Assistant are organized in sub-folders in the www folder, which sits in the root of my Home Assistant configuration.  The path to the coffee sounds is `/www/sounds/coffee/` and they are labeled sequentially starting with the value `10` (it just makes it easier to not have to program for a leading zero between 1 and 9) and incrementing up one value for each audio file. In that same folder are sub-folders containing additional sounds that play during special occasions.  I have a `/christmas`, `/halloween`, and `/thanksgiving` folder.  Each of those folders contains a sequence of audio files, one for each commercial, etc., all labeled 10 through whatever.

You can see this structure [here](https://github.com/arretx/HomeAssistant/tree/master/www/sounds/coffee).

### Function Node Example

One of my function nodes is entitled "Set Global Variables."  Call yours whatever you'd like.  Here is what it contains:

``` python linenums="1"
// This function creates an array of values all assigned to a global context 
// variable which can be accessed from any node throughout NodeRED.
```
#### Define Message Topic
``` python linenums="3"
msg.topic = "globalVariables" // Set the topic to "globalVariables"
```
As the message passes through this node, the value msg.topic is updated to the string value `globalVariables.`

#### Store Every State and Service from Home Assistant as a Global Variable array
``` python linenums="4"
const gHomeAssistant = global.get('homeassistant').homeAssistant; // Extract every state and service from HA into an array.
```
This will store an object in a global variable called `homeassistant` which can be viewed in NodeRED's context data tab in the NodeRED sidebar.  Every state and service will be available in this variable.

#### Define a Constant
const HAUrl = "http://192.168.1.101:8123" // Home Assistant's internal URL
var melanie = gHomeAssistant.states['person.melanie_griffith'].state;
var jon = gHomeAssistant.states['person.jon_griffith'].state;

if (melanie == "home") {
    var melanieHome = true;
} else {
    var melanieHome = false;
}

if (jon == "home") {
    var jonHome = true;
} else {
    var jonHome = false;
}

var states = { 
    "holiday" : gHomeAssistant.states['calendar.holidays_in_united_states'].state,
    "holidayName" : gHomeAssistant.states['calendar.holidays_in_united_states'].attributes.message,
    "melanieHome" : melanieHome,
    "jonHome" : jonHome,
    "soundFxURL" : HAUrl + "/local/sounds",
    "soundFxPath" : "/config/www/sounds/",
    "HAUrl" : HAUrl
    };

global.set("globalVariables", states);
var globalVariables = global.get("globalVariables")

//msg.payload = gHomeAssistant;
msg.payload = globalVariables;
return msg;
```
