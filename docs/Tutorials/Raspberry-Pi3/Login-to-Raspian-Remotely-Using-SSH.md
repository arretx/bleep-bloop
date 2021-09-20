## Steps

1. Go to Preferences -> Raspberry Pi Configuration
2. Select the `Interfaces` tab.
3. Select `Enable` for SSH.
4. Click OK.

## Find the IP Address of your Raspberry Pi3

When your Pi3 connects to your WIFI network, your router issues an address for the Pi3.  That address is dynamic, meaning it may or may not change in the future.  To find this address:

1. Click the Command Prompt button on the toolbar.
2. When the command prompt opens, you'll see something like 

    ```
    pi@raspberrypi:~ $
    ```

    This is your command prompt for the home folder of the user `pi`.

3. At the prompt, enter the following command: `ip addr show` then press Enter.

    The resulting mish mash on the screen contains a list of adapters.  On the left, you'll see 1, 2, 3 going down the left side of the screen.  You're looking for the `wlan` entry.  Under this entry you're looking for something that looks like `inet 192.xxx.xxx.xxx` where the x's have numbers.

    This is your Raspberry Pi3 address on the network.  Knowing this, you can now use SSH on any other linux or MacOSX computer (or Putty on Windows).

4. Open terminal from any other linux or MacOSX based computer and type the following:

    ```
    ssh pi@<your_ip_address>
    ```

5. Press enter and then `yes` to the next prompt about security.
6. Type your Raspberry Pi3 password (the one you used when you setup the Pi3 in the first place) and press enter.
7. Voila!  You're now looking at the same prompt that you saw on the Pi3, but you're now controlling it from somewhere else.

You may want to look into setting up security keys on the PI so you can login without using a password.  Google it!


