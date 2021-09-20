There are a few key things to note when you setup Syncthing in Ubuntu.  Here are my specific answers to my own questions about how to set it up every time I need to do so:

## Auto Start / Run Syncthing as a System Service

!!! note
    This works on both Ubuntu 16.04 and 18.04, and probably many others, but I only have first-hand experience with the two listed here.

I set syncthing up as a service that runs regardless of whether or not someone is logged in.  To do this you have to:

- Create a syncthing@.service file using nano, or something like nano, in the '/lib/systemd/system/' path on your server. You can download this file from Github. https://github.com/syncthing/syncthing/tree/master/etc/linux-systemd/system as a reference, or just copy it directly into the folder.

- Create a user on your system.  Mine is simply called `syncthing` with a home folder.  You can also use a pre-existing user if you choose.  Just know that **you may need to screw around with permissions in order to get folders to sync with each other**.
- Enable the syncthing@.service:

    `sudo systemctl enable syncthing@<username>.service`
    
    Replace `<username>` with the username you created.
    
- Start the service:

    `sudo systemctl start syncthing@<username>.service`

- In the user's home folder, find the .config folder and drill down into that folder until you find the config.xml file.  Edit that file and change the listening port to your preferred port and the 127.0.0.1 ip address to your machine's actual internal IP address.

- You should be able to load up Syncthing in a browser using your server's IP address : port number (http://whateverip:port)

- Once you've loaded it, follow some steps to secure the interface and apply HTTPS if you choose.

- From whatever other machine you're using that has Syncthing installed, add the new server as a Remote Device.  The long ID number should show up as an available device running near you.

- Go back to the Syncthing interface at the server address and wait until you're prompted to add the remote device.
- Complete the relationship.

Any folder you need to share on the server is going to need to have read/write permissions for the user you chose to create the service with, and the folder is going to need to have the correct permissions for allowing user/group read/write/execute permissions also.

Rather than taking ownership, especially if this is happening in a folder that's NOT owned by the syncthing user, you can use ACL's to _define rights for an additional user_.

If you need to give user A full access to user B's folder structure, just use the `setfacl` utility in Linux to do so.  An example of a command that would work would be something like this:

`setfacl -R -m user:<username>:rwx /path/to/folder/`

Now, you should have no problem synchronizing between whatever and whatever.  :)