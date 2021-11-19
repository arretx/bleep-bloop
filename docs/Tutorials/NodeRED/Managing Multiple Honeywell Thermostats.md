# Managing Multiple Honeywell Thermostats

!!! note
    The thermostats used in this tutorial are all Honeywell wifi thermostats that require a cloud connection to MyTotalComfortConnect.com.  Certain aspects of this automation may work for your situation, but know that these are the types of thermostats we're dealing with.  You still may find value in this article even if you're using a completely different brand of thermostat.

## The Problem

At my church, I noticed a problem.  In our sanctuary, we have 5 thermostats.  When we first occupied the space, I observed something that drove me insane.  One person, or multiple people, would walk around the entire perimeter of the sanctuary adjusting each thermostat individually.  Every.  Day.  The first problem was that they weren't all wired up to the same location.  That would have been basic, common sense.  Someone didn't think of this, so it didn't happen.

_Fortunately_, the thermostats are all wireless, so they can all be controlled remotely.  _Unfortunately_, that can only be done through the MyTotalComfortConnect.com website and through an app that connects to that website to control the devices locally.  It seems a shame to be able to connect your thermostats to your network, then be required to sign in to a third party website to actually control them, but that's the way it is.

## The Solution

Through the Honeywell integration in Home Assistant, we are able to provide Home Assistant with the state information that it needs to monitor the thermostat settings.  Keep in mind, Home Assistant isn't monitoring the devices themselves.  Home Assistant is connecting to the cloud service to capture the data that the thermostats have sent through the internet, and control changes to those settings through the cloud service.

!!! important
    Honeywell's MyTotalComfortConnect.com service will limit the number of calls to their API from third party applications like Home Assistant.  I know this because I made an error in how I built the flow originally, and I ended up causing a loop that kept requesting changes to the thermostats through their service.  **Unfortunately, I don't know what their limits are, but they do exist.**  Since fixing the problem in NodeRED, I haven't been locked out of their service since.

## How It Works

Home Assistant can be configured to change just about any of the states or attributes that it registers when you first integrate the thermostats with the MyTotalComfortConnect.com website using the Integrations in Home Assistant.  At the same time, Home Assistant will be updated with any changes that are made from the website, which will listen to any changes that are made manually at the thermostats.

Basically, if you change the thermostat manually, it will update the website, then Home Assistant will receive the change information and update the entity's state and/or attributes.

## Explaining the NodeRED Flow

### Ingredients

These are the nodes that are used in the flow:

#### Home Assistant Nodes
* 2 Trigger State nodes
* 1 Get Entities node
* 2 Call service nodes

#### Additional Nodes

* 1 Trigger node - (Function)
* 3 Change nodes - (Function)
* 2 Switch nodes - (Function)
* 1 Split node - (Function)
* 2 Stoptimer nodes - (Function)
* 1 Simple Queue node - (Storage)

### Flow Walkthrough

I'll explain each node in the order in which the messages are sent for this automation, starting with the very first node:

#### Step 1: Trigger State
The flow needs to know when any of the 5 thermostats' states or attributes are updated either from within Home Assistant's UI, or from the Honeywell website (remember, the website is updated by Home Assistant -or- by manually changing the thermostats individually).

The trigger state watches for changed temperature settings from any of the climate.sanctuary* entities.  They are strategically named so it's easier to monitor multiple devices by using wildcards.  Any device that's named climate.sanctuary_xxx will be evaluated.

The state of the thermostats is a string, so we leave that as it is.

We need some conditions to ensure we don't read too many changes or have any false positives.  After all, a state change doesn't only occur when the temperature is adjusted.  If the unit's fan mode is changed, for example, that will also trigger the state change for the entity, and if the temperature hasn't changed, we don't need to be running the flow to change the temperature to the same temperature.  So...

* Condition 1: Check the new_state.attributes.temperature and make sure it is NOT the same as the previous temperature ($prevEntity().attributes.temperature).
* Condition 2: Don't do anything if the temperature in the message is empty (null.)
* Condition 3: Don't do anything if the old temperature in the message is empty (null.)

That takes care of _watching the thermostats_.

#### Step 2: Trigger State
The trigger state prevents additional changes from being noticed until after the current change is completed.  We'll use a change node at the end of the flow to reset this node so it will listen to new messages.

#### Step 3: Get Entities
We move to the next node which captures all of the state and attribute information of all of the climate.sanctuary* entities in Home Assistant.  The output type is an array which we'll split into individual messages in the next few nodes.

#### Step 4: Change Node
This is our first change node.  We use this to add some information to the msg.payload (the message moving down the flow.)  We set a `target_temp` in the msg, and we assign it the value already found in `msg.data.event.new_state.attributes.temperature`.

#### Step 5: Split Node
The split node splits the array into individual messages.  The array length is 5 which we know from the number of thermostats returned from Step 3: Get Entities.  If this number were to increase or decrease, we would need to update this node to reflect the correct number of splits.

We split using a fixed length of 1 so we end up with 5 independent messages.

#### Step 6: Switch Node (Omit the Original Entity)
There's no sense in updating the thermostat we originally adjusted because it was the one that triggered the flow in the first place.  If we did, we would end up in a screwy loop which would cause problems with the cloud service.

This switch node doesn't evaluate the msg.payload.  It evaluates the msg.topic, which in this case is going to simply be the name of the entity that triggered the entire flow in the first place.  So, we filter out one of the 5 messages by comparing the `msg.topic` to the `msg.payload.entity_id` in each of the 5 messages that we split in the previous node.

#### Step 7: Simple Queue
We use the simple queue because we need each change to stand in line before it's sent to the service call and on to the MyTotalComfortConnect website.  Except for the first message, which is passed through immediately.  So, of the 4 messages (4 thermostats that need to be updated), one will go immediately, and 3 will wait in line for each message to be completed before moving to the next in line.

The `simple queue` node looks for a msg.trigger property with a value to be received before it will release the next message in the queue.  That will be provided after the current message is sent through the entire flow.

There's a count under the Simple Queue node that will decrease while this entire process is happening.

#### Step 8: Service Call 1
The first service call sets the fan mode for the next thermostat to Auto.

#### Step 9: Stoptimer
A stop timer simply holds the message until the timer runs out or until it's manually released by resetting the timer.  We just set a static 5 seconds in this case.

#### Step 10: Service Call 2
This is the service call that sets the thermostat's temperature.

#### Step 11A: Change Node
There's a change node that assigns a value of 1 to the `msg.trigger` property and routes that information back to the orange Message Queue node to release the next thermostat setting.

#### Step 11B: Split Node
We now test to see if that was the last update.  When all thermostats are updated, we'll re-enable the Trigger State node in Step 2 so new changes to the thermostats can kick off an entirely new process.  If there are still more thermostats that need to be updated, the message will stop at the split node until the right conditions are actually met.

#### Step 12: Stop Timer
Another 5 second stop timer is added just as a buffer to ensure things don't get clogged up in the system.  This could probably be fine tuned, but 5 seconds seems to work.

### Flow JSON
If you import this JSON from your clipboard into NodeRED, you can quickly make changes to it to suit your needs.  There may be some nodes that aren't yet installed in your system that you'll need to use the Palette to install, but once you do, they'll work.  Your entities may also be named differently, so make your adjustments accordingly.

Simply copy this entire code to your clipboard, then import into NodeRED.

~~~
[{"id":"a09aa918.4dced8","type":"ha-get-entities","z":"f2a57f2f.5ea0a","name":"Get Thermostats","server":"b650d991.c980b8","version":0,"rules":[{"property":"entity_id","logic":"is","value":"climate.sanctuary*","valueType":"re"}],"output_type":"array","output_empty_results":false,"output_location_type":"msg","output_location":"payload","output_results_count":1,"x":610,"y":680,"wires":[["96479c8a.b8749"]]},{"id":"ead552d2.6a74e","type":"split","z":"f2a57f2f.5ea0a","name":"Split Array into individual messages","splt":"5","spltType":"str","arraySplt":"1","arraySpltType":"len","stream":false,"addname":"","x":680,"y":580,"wires":[["5ba820b2.0e9a3"]]},{"id":"5ba820b2.0e9a3","type":"switch","z":"f2a57f2f.5ea0a","name":"Do Not Include Original Triggered Entity","property":"topic","propertyType":"msg","rules":[{"t":"neq","v":"payload.entity_id","vt":"msg"}],"checkall":"true","repair":false,"outputs":1,"x":680,"y":640,"wires":[["441d9d54.23ce34"]]},{"id":"96479c8a.b8749","type":"change","z":"f2a57f2f.5ea0a","name":"Define Target Temp in MSG","rules":[{"t":"set","p":"target_temp","pt":"msg","to":"data.event.new_state.attributes.temperature","tot":"msg"}],"action":"","property":"","from":"","to":"","reg":false,"x":680,"y":520,"wires":[["ead552d2.6a74e"]]},{"id":"441d9d54.23ce34","type":"simple-queue","z":"f2a57f2f.5ea0a","name":"Queue Up Temperature Change Messages","firstMessageBypass":true,"bypassInterval":"0","x":1070,"y":520,"wires":[["47593634951bcada"]]},{"id":"268a1df3.bd42f2","type":"api-call-service","z":"f2a57f2f.5ea0a","name":"Set Temperature on Unit","server":"b650d991.c980b8","version":3,"debugenabled":false,"service_domain":"climate","service":"set_temperature","entityId":"{{payload.entity_id}}","data":"{\"temperature\":\"{{target_temp}}\"}","dataType":"json","mergecontext":"","mustacheAltTags":false,"outputProperties":[],"queue":"none","x":1470,"y":600,"wires":[["6c472b35.1ce404","84cf5314.a0e02"]]},{"id":"6c472b35.1ce404","type":"change","z":"f2a57f2f.5ea0a","name":"Trigger Message Queue Next Message","rules":[{"t":"set","p":"trigger","pt":"msg","to":"1","tot":"num"}],"action":"","property":"","from":"","to":"","reg":false,"x":1470,"y":520,"wires":[["441d9d54.23ce34"]]},{"id":"84cf5314.a0e02","type":"switch","z":"f2a57f2f.5ea0a","name":"Was that the last message?","property":"_queueCount","propertyType":"msg","rules":[{"t":"eq","v":"0","vt":"num"}],"checkall":"true","repair":false,"outputs":1,"x":1300,"y":660,"wires":[["1097b14a.24510f"]]},{"id":"1097b14a.24510f","type":"stoptimer","z":"f2a57f2f.5ea0a","duration":"5","units":"Second","payloadtype":"num","payloadval":"0","name":"Wait 5 Seconds","x":1540,"y":660,"wires":[["522e42e2.f0b81c"],[]]},{"id":"47593634951bcada","type":"api-call-service","z":"f2a57f2f.5ea0a","name":"Set HVAC to Auto","server":"b650d991.c980b8","version":3,"debugenabled":false,"service_domain":"climate","service":"set_fan_mode","entityId":"{{payload.entity_id}}","data":"{\"fan_mode\":\"auto\"}","dataType":"json","mergecontext":"","mustacheAltTags":false,"outputProperties":[],"queue":"none","x":1070,"y":600,"wires":[["f4aebb6250ee93e3"]]},{"id":"f4aebb6250ee93e3","type":"stoptimer","z":"f2a57f2f.5ea0a","duration":"5","units":"Second","payloadtype":"num","payloadval":"0","name":"Wait 5 Seconds","x":1260,"y":600,"wires":[["268a1df3.bd42f2"],[]]},{"id":"34f62620.b3e8ca","type":"trigger","z":"f2a57f2f.5ea0a","name":"Stop State Triggers Temporarily","op1":"","op2":"","op1type":"pay","op2type":"pay","duration":"0","extend":false,"overrideDelay":false,"units":"ms","reset":"","bytopic":"all","topic":"topic","outputs":1,"x":310,"y":620,"wires":[["a09aa918.4dced8"]]},{"id":"96c66197.9fb4c","type":"trigger-state","z":"f2a57f2f.5ea0a","name":"Watch for Changed Temperature Setting","server":"b650d991.c980b8","version":0,"exposeToHomeAssistant":false,"haConfig":[{"property":"name","value":""},{"property":"icon","value":""}],"entityid":"climate.sanctuary*","entityidfiltertype":"regex","debugenabled":false,"constraints":[{"targetType":"this_entity","targetValue":"","propertyType":"property","comparatorType":"is_not","comparatorValueDatatype":"jsonata","comparatorValue":"$prevEntity().attributes.temperature","propertyValue":"new_state.attributes.temperature"},{"targetType":"this_entity","targetValue":"","propertyType":"property","comparatorType":"is_not","comparatorValueDatatype":"re","comparatorValue":"^null$","propertyValue":"new_state.attributes.temperature"},{"targetType":"this_entity","targetValue":"","propertyType":"property","comparatorType":"is_not","comparatorValueDatatype":"re","comparatorValue":"^null$","propertyValue":"old_state.attributes.temperature"}],"outputs":2,"customoutputs":[],"outputinitially":false,"state_type":"str","x":180,"y":520,"wires":[["34f62620.b3e8ca"],[]]},{"id":"c769fc61069c57c6","type":"comment","z":"f2a57f2f.5ea0a","name":"Auto update all thermostats.","info":"1. A trigger node listens for events on all entities that are named climate.sanctuary* where * = wildcard.\n\nThe conditions that must be met to proceed:\n\nAll units must not report a previous or new temperature setting of null (nothing).\n\nThe new temperature setting is not the same as the old temperature setting.\n\n\n","x":140,"y":480,"wires":[]},{"id":"51832ef350162fdd","type":"comment","z":"f2a57f2f.5ea0a","name":"Wait for process to complete...","info":"This prevents the flow from triggering itself while updating the other thermostats.\n\n","x":310,"y":580,"wires":[]},{"id":"f1de7660.eb13c8","type":"change","z":"f2a57f2f.5ea0a","name":"Reset Trigger Blocking","rules":[{"t":"set","p":"reset","pt":"msg","to":"1","tot":"str"}],"action":"","property":"","from":"","to":"","reg":false,"x":280,"y":740,"wires":[["34f62620.b3e8ca"]]},{"id":"c5edb66a.40b5b8","type":"link in","z":"f2a57f2f.5ea0a","name":"Inbound Unblock Trigger","links":["522e42e2.f0b81c"],"x":55,"y":740,"wires":[["f1de7660.eb13c8"]]},{"id":"522e42e2.f0b81c","type":"link out","z":"f2a57f2f.5ea0a","name":"Re-Route to Unblock Trigger","links":["c5edb66a.40b5b8"],"x":1675,"y":660,"wires":[]},{"id":"b650d991.c980b8","type":"server","name":"Home Assistant","version":1,"addon":true,"rejectUnauthorizedCerts":true,"ha_boolean":"y|yes|true|on|home|open","connectionDelay":true,"cacheJson":true}]
~~~






