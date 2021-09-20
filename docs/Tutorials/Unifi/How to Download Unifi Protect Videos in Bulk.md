* Login to Ubuntu Server
* Run the following command: 

`
docker run unifitoolbox/protect-archiver download --address=192.168.1.1 --username=archive --password=<password> --cameras=all --skip-existing-files --start=2021-02-01 --end=2021-07-27 /tmp
`

Parameters:

--address = ip address of Dream Machine Pro  
--username = user you've created on UDMPro with Limited Admin rights.  
--password = that user's password (I used local login only for this user)  
--cameras = which cameras you want footage from  
--skip-existing-files = self explanatory  
--start and --end = the date range for the video you want to download.   


