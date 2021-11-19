# How to Build an Efficient NodeRED Automation to Play Random Sounds for Specific Events in Home Assistant

## Intro

This tutorial will teach you a more advanced way to build a flow than just using a simple trigger, sound file, and media player.  In the past, I've simply created multiple flows, all doing basically the same thing: watch for a trigger, look at a condition or two, and then play the file.

As my system has grown, I've needed to figure out more efficient ways to create flows so I'm not cluttering up my workspace and losing track of the automations, and most importantly, duplicating work.

This setup will show you how to configure NodeRED with a single flow to play different sets of random sounds based upon the events that occur.

## Example

Our real world example involves a SonOFF Basic V2 WIFI switch attached to a simple drip coffee maker with no built in programming.  It's either on, or off.  Our original problem to solve was rembering to turn off the coffee maker.  Once it completed the drip, it remained on, and we had no indication that the coffee was ready, so sometimes it would sit there for hours, especially if we brewed it in the middle of the day.  The solution was to have Home Assistant automatically turn it off when it was done brewing.  Since there's no feedback from the coffee maker itself, we had to sample the timing of the drip cycle when starting with cold water, to ensure that the coffee maker was on long enough to complete the brew, and then run a timer in our flow that would pass the payload on to a switch.turn_off service call when the timer expired.

To enhance the experience, I thought it would be nice to have our media_player(s) play a random coffee commercial in the form of an MP3 file stored in the www folder structure.

## Prerequisites

  * Home Assistant up and running.
  * Full access to your Home Assistant `/config` folder.
  * A working `/www` folder under config that enables public access (https://yourhomeassistant.com/local/)
  * A media_player configured in Home Assistant
  * NodeRED add-on up and running.
  * A list of MP3 files you can use for testing.

## Initial Setup Steps

### Preparing Sounds  
  
  1. Create a `/sounds` folder under `/config/www/` (You can organize this however you want...it doesn't have to be named `/sounds`, but for this tutorial, that's what we're calling it.)
  * Create a folder called `test_switch` under the `/config/www/` folder.
  * Create two folders below the `test_switch` folder labeled `/on` and `/off`.  This allows us to create a set of sounds for both "on" and "off" stage changes for this entity.
  * Download or create 6 MP3 files.  6 is just an arbitrary number.  You could use 22 billion files if you wanted.
  * Rename those files sequentially (10.mp3, 11.mp3, 12.mp3 and so on)  I originally started at 10 to avoid having to do math to program for a leading zero on numbers 1 - 9. (This has since been noted as an unnecessary need, so you can actually name them whatever you want.)
  * Copy 3 of those files into the `/on` folder and 3 into the `/off` folder you created in step 3.
  * Test one of those sounds by loading it into your browser using your internal url and the path to the www folder.  This would look something like `https://192.168.X.X:8123/local/sounds/test_switch/on/10.mp3`  Make sure you use your local HA ip address here.  If your browser doesn't load the sound, there's a problem somewhere with your URL.  If you hear the sound, congratulations, you can proceed.

### Create a Test Switch for NodeRED

  1. Create an `input_boolean` in your home assistant configuration.  You'll use this to test your NodeRED flow.  

    ``` python linenums="1"
    input_boolean:
      test_switch:
        name: Test Switch
    ```

  * Check your Home Assistant configuraton before restarting Home Assistant to ensure your syntax is correct, then restart.  You'll now have a `input_boolean.test_switch` in your entities.
  * Go to the `Configuration` menu item in Home Assistant and scroll to the bottom.  Click `Customizations`.
  * Type the word `test` in the Entity search box.  You should see your new `input_boolean.test_switch`.  Select it.
  * At the bottom of the options presented, click `Pick an attribute to override` then select `Other`.
  * In `Attribute name` type `audio_enabled`.
  * In `Attribute value` type `true`.  This creates a custom attribute for your new entity with a string value (note, not a boolean value) of "true."  This is used to filter out state_change events in NodeRED so we only pass the ones we want through the flow.  We're basically saying, hey NodeRED, this entity should have sound effects.
  * Click `Save.`

## NodeRED Flow

Now that we have an entity that we can monitor in NodeRED, it's time to create the flow for NodeRED.

  1. Open the Node-RED user interface from within Home Assistant (Add-ons).









