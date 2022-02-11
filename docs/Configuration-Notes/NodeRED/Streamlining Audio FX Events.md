|NodeRED Tab|
|-|
|Development|

1. **Watch All State Changes**

    Monitors all state changes in Home Assistant.

1.  **Switch:** Is this a sound enabled event?  

    In Home Assistant, we can add a custom attribute to entities that we want to be identified by this node as "audio_enabled."  This is a custom definition.  Any state change by an entity that has this custom attribute will pass through to the next node.

    1. **Property:**  

        We test for this custom attribute in the Property field by using **msg.** `payload.event.old_state.attributes.audio_enabled` and we test for == against the `string` value of `true`.

        If true, the msg continues to the next node.

1. **Change Node**: Modify Payload for Sound Effect

    We have some information that we need to add to the payload for subsequent nodes.

    1. **Internal URL**

        The network location of Home Assistant added to the payload from a global variable which is defined in a different flow. If the network location of Home Assistant ever changes, we don't have to worry about updating flows because this is pulled directly from the Home Assistant configuration.

    1. **Public Path to Audio Files**

        This is a string value of `/local/sounds/` and will be different for your situation, unless your situation is the same.

    1. **Private Path to Audio Files**

        This is the internal URL to the audio files, which is `/config/www/sounds/`.
    
    1. **Sound Theme**

        This is added from a global variable which is updated daily at midnight based upon a Function node with some javascript that defines custom theme settings.  For example, in December, the theme is Christmas, or `christmas` and thus, this is injected as a portion of the internal URL path when pointing a media player to the audio files.

    1. **J-expressions**

        The next three settings in this node are J-expressions.

        1. **Create the path to the sounds as a string**

            In `msg.payload.sound_folder` we store the value from:
            ~~~
            payload.sound_theme !="" ? $substringAfter(payload.entity_id,".") & "/" &	 payload.sound_theme & "/" & $string(payload.event.new_state.state) : $substringAfter(payload.entity_id,".") &	 "/" & $string(payload.event.new_state.state) `
            ~~~

            Let's break this down:

            1. `$substringAfter(payload.entity_id,".")`

                This becomes just the entity name of the device.  For example, if the entity is `switch.coffee_maker` then the result from this would just be `coffee_maker`.

            1. `$string(payload.event.new_state.state)`

                This adds the state of the entity as a string value to the path.  In the case of the coffee_maker, the resulting string would either be `on` or `off` and this is used to choose a subfolder of the path to the sounds for a random selection of sounds for just the `on` state and a different set of sounds for the `off` state.

            The entirety of this JSONata expression is to see if there's a current theme.  If so, add the theme name to the path of the sounds.  If not, leave it alone and just use the default sounds for that entity.

        1. **Tell the flow where to look to count the number of sounds for this event**
            
            1. `$string(payload.sound_internal_path & payload.sound_folder)`

                Stored in `msg.payload.file_count_path`

                Create a string value for the internal path to the location of this event's audio files.  This is used by the fs-ops-dir node to count the number of MP3 files in that folder so it knows the range for generating a random number.

        1. **Payload Entity Suffix**

            This simply stores the name of the entity, i.e. `coffee_maker` in the `entity_suffix` portion of the payload.

1. **FS-FILE-OPS**

    This node lists all of the files in a directory on the host system.  The settings are as follows...

    1. **Name:** Whatever you want.
    2. **Path:** this would be `msg.payload.file_count_path` as defined above in the JSONata expressions section of the previous flow.
    3. **Filter:** This is a string value of `*.mp3` to list all MP3 files in the folder.  This can be whatever you want it to be.
    4. **Directory Property:** This is `msg.payload.file_count_array` which stores the resulting list of files in the payload as `file_count_array`.

1. **Change Node** Add the file count to the payload.

    This node looks at the _length_ of the value in `msg.payload.file_count_array` and stores the value as a number in `msg.file_count` using a SET command.

1. **Random Item**  Pick a random audio file.

    This uses the `random-item` node to choose one of the values stores in `msg.payload.file_count_array`.  This single node solved the problem of needing name the audio files sequentially starting at 10 as it can now just pick whatever the name of the file is and address it when assembling the final audio url in the media play call.

    The result is stored in `msg.payload.sound_file`.

1. **Change Node** Add the full path to the media file to the msg for use by the service call to the media player.

    This is a JSONata expression that sets a string value in `msg.payload.media_url` to:

        $string(payload.internal_url & payload.sound_public_path & payload.sound_folder & "/" & payload.sound_file)

        
    Piecing together all of the elements, we end up with a full path to the exact audio file to play for the event.

1. **

                

    




