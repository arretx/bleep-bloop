As I've configured my server to use LVM, I'm able to adjust the available space to whatever I choose.

In this case, I have 4 single 1TB drives (unfortunately, one of them has failed, so I'm only working with 3).

In Webmin, under `Hardware --> Logical Volume Management` I have a single volume group that consists of 3 of those physical disks.  There are 3 logical volumes.

* Storage 1 (1.32TB)
* Storage 2 (1.27TB)
* ubuntu-lv (System files | 50GB )

There's really no rhyme or reason to this setup, but that's what's happening.

In `System - Disk and Network Filesystems` I have two mount points:

* /arabian
* /quarterhorse

In `Other - File Manager` I can see both "arabian" and "quarterhorse."

On `Arabian`, the owner is root:users with 0755 permissions.  As I understand, this means the user root is the owner, and the "7" means the user `root` can read, write, and execute the file while `any` user in the group `users` will only be able to read and execute the file.  Correct me if I'm wrong.

## Question 1: Is this True?

Armed with that information, on the surface, it would seem that if I share `Arabian` through Samba, then anyone on the network will be able to read and execute the files if they know the path to the share.  But, it also seems that Samba has a role in who can and cannot RWX files and folders regardless of the underlying file ownership and permissions.

Is this true?

## Question 2: When creating a new share in Webmin...

** Create File Share Dialog **

I want to understand exactly the implications of each field here and what their impact is, and whether or not information is required in each or not.

![filesharedialog](/img/createfileshare.png)

1. ** Field 1: Share Name **: I've struggled with this if a folder already exists.  Should it be the same name as the folder I'm sharing or does it not matter?
2. ** Field 2: Directory to Share **: No brainer.  Got that one figured out.
3. ** Field 3: Automatically create directory **:  Not sure about this.  If the directory already exists, why would I need to automatically create the directory?
4. ** Field 4, 5 & 6: Create with owner, etc**: Seems that if I'm not creating a folder, these settings would be irrelevant, but they're filled out automatically.  Not sure what to make of that.
5. ** Field 7 & 8: Available / Browsable**: Obvious.
6. ** Field 9: Share Comment:** A notes field, I guess.

Now, once I hit `Create`, the share is created, but is inaccessible by anyone, despite showing `Read only to all known users.`  This doesn't make much sense.

## Question 3: Edit File Share

The `Edit File Share` dialog opens up when you click on the share in the previous `Samba Windows File Sharing` screen with new options called `Other Share Options`.

### Security Access and Control (Edit Security)

1. ** Writable? **: In this case, typically yes.
2. ** Guest Access? **: No idea what this does.
3. ** Guest Unix User **: Who is this person `nobody` that's automatically filled in?
4. ** Limit to possible list? **: What is the possible list and what is being limited?
5. ** Hosts to allow/deny **: No explanation needed.
6. ** Revalidate users? **: When?  When does revalidation happen and what is being revalidated?
7. ** All of the remaining options **: There seem to be too many options here for a simple file share.  
    * I have no idea how Valid or Invalid groups/users affects a share.  Valid for what?  Read? Write? Execute?  Not sure.
    * The Possible users/groups...possible what?  How is it "possible" that a user is or isn't something?

What I'm finding is that if I don't add a group like `users` to the `Read/write groups` or add specific users to the `Read/write users` field, AND also set the file permissions of the file in the file explorer to mirror this information, then a user will be able to see the share on the network but won't be able to see into the shared file.

It would be great if I could clear up what is required and what is just there for extra features (slash complications).

### File Permissions

This is what's really confusing me.  If I set file permissions in Linux, why do I have to set more file permissions in Samba?

1. **New Unix file mode**: Is this the resulting `chmod` when a user creates a file in the share, regardless of the folder security settings?
2. **New Unix directory mode**: Same question, but I believe it's referring to folders rather than files.
3. **Directories not to list**: Seems like a simple exclusion list to hide certain folders under the share.
4. **Force Unix user/group**: Does this set the unix user and group ownership on files created in this share regardless of who creates them?
5. **Force Unix File/Directory mode**: This seems to contradict the first two fields.  Is this an override setting rendering the first two fields useless?



