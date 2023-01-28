## **Don't Be Intimidated**

NodeRED makes controlling your home's logic _so dang much easier_ than yaml, you'll wonder why you didn't start using it earlier.  Don't get me wrong.  Understanding how to write automations using yaml and scripts in Home Assistant is a valuable tool to have under your belt, but once you use NodeRED, you'll realize how much more complex your automations can be.

!!! note
    A few things are assumed before you continue.  1. You operate in the HASS.io environment.  2. You have installed the NodeRED add-on for HASS.io and you can get to it.  Beyond that, there may be a few nodes that you'll need to install from within NodeRED.  Otherwise, you're ready to get started.

## The Why

Ultimately, the reason I'm doing this is because I'm _not_ a programmer and I _need_ a way to visualize the complicated logic that I think of. Often I want things to happen that end up way more complicated than they need to be, and require streamlined processes that eliminate duplication.

## Variables in NodeRED

In NodeRED, you can create arbitrary variables that store information based upon the messages that flow through the nodes.  You can store information in variables that are accessible by a single **node**, an entire **flow**, or **globally**, meaning the whole of NodeRED.

