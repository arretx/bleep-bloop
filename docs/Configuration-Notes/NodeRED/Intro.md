Using the Function node in the HASS.io NodeRED add-on is a great way to declare global variables for your entire NodeRED configuration so you have access to specific information that you've customized for your setup.

Since there are 3 different contexts (Global, Flow, and Node), you'll have to decide how you want to organize your variables.

If you have a value that you want to hold permanently while your system is up and running that you want to be able to access in any of your flows, then setting a global variable will help you achieve that goal.

