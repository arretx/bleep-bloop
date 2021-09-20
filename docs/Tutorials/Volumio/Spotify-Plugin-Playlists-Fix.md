!!! warning
    As validated on Monday, June 1st, 2020, the information on this post no longer applies.  According to first hand experience, the Spotify plugin is failing to authenticate in Volumio.  If you have a solution, please comment.

## Introduction

This applies to the Volumio media server on a Raspberry Pi3 utilizing the Spotify2 plugin.

The current Volumio release can utilize a Spotify plugin to access your Spotify music preferences, however, the Spotify plugin needs to be tweaked after it's installed to ensure that you can see your playlists in Volumio.

This information is good as of March 8th, 2019, and came from a post by skikirkwood which can be found in [this](https://forum.volumio.org/spotify-playlists-not-working-anymore-t11918.html) thread.

## Steps

1. Click [this link](http://54.86.144.136:8888) and Login with Spotify.  The resulting page should ask you to login to Spotify and then authenticate access for the Spotify Volumio plugin and then will show you your new Auth tokens.
2. Copy the raw code from [this](https://github.com/skikirkwood/volumio-plugins/blob/playlisturi/plugins/music_service/spotify/index.js) repository file on Github and paste it into your preferred text or code editor.
3. Change line 451.  Where it reads `var refreshToken = 'xxxxxxxx';` replace the x values with your new *REFRESH* token.
4. Save this file in a temporary location.
5. Login to your Volumio server using SSH.
    
    ```
    ssh volumio@volumio.local or ssh volumio@youripaddress
    ```

6. Once you see the prompt `volumio@volumio:~$`, change to the spop folder

    `cd /data/plugins/music_service/spop/`

7. Make a backup of the current `index.js` file.

    `cp index.js index.js.bak`

8. Delete the old index.js file: 
    
    `rm index.js`

8. Enter: `sudo nano index.js`
9. Paste the contents of the entire index.js file you downloaded and edited in steps 2 & 3 into the nano editor.
10. `CTRL-X` to save, Enter to confirm.
11. Restart Volumio from the `Shutdown` menu in the Volumio web interface.

I've tested this as of July 27th, 2019 and it in fact works.