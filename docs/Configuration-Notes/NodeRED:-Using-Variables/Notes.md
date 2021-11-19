# NodeRED Sound Effects Configuration for Home Assistant Events

The process begins with an `events: all` node set to watch only `state_changed` event types.  To differentiate between events that are supposed to trigger sound versus events that aren't, we use a `switch` node after the `events: all` node to test for a custom attribute, which we define for each entity that should trigger a sound effect.  We add a custom attribute called `audio_enabled` and assign it the value `true` to every entity that we want to participate in the sound effects flow(s).

## Test Case

Our test case will use the `switch.coffee_maker` entity.

When the `switch.coffee_maker` entity changes states from off to on, we need NodeRED to know this and act accordingly.  The `events: all` node will see the `state_change` and pass the payload on to the `switch` node.  The `switch` node will look for the custom attribute `audio_enabled` in the attributes of the payload.  If the value exists and it is set to true, the message will pass to the next node.

Using a `change` node, we will remove the domain portion of the entity_id and change the payload to reflect only the suffix of the entity ID..  In this case, `switch.coffee_maker` will be converted to `coffee_maker`.



soundFxPath = /config/www/sounds/

HAUrl = "http://192.168.1.101:8123" (this can be found in the config variable)

soundFxURL = HAUrl + "/local/sounds" = http://192.168.1.101:8123/local/sounds


soundFxCoffeePath = /config/www/sounds/coffee/ (for counting available sound files)








