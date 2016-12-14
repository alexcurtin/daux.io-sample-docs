## Backing up an environment

### CMS Folder & Contents

SSH into the server you wish to backup, either Jibestream UAT or Jibestream Production. The binary `zip` has already been installed.

	$ zip -r /tmp/westf.zip <folder location>

This will create a .zip backup of the cms directory and save it to the servers `/tmp` directory. From here, you can rsync the .zip down to your local host to upload to a box account or s3 bucket, to be given to Jibestream.


### DB Schema

Open PGAdmin3 and connect to the database you wish to take a backup of. Under the `Schema` drop-down select `Novomap8080` and right-click the `Novomap8080` and select Backup...

![alt text](/assets/BackupDialogBox.png)

Enter in the file name, select UTF8 for Encoding and select the proper owner of the schema (Novomap8080) from the drop-down list.

![alt text](/assets/BackupSchema.png)

On the second tab (Dump Options #1) check the first three checkboxes (Pre-Data, Data, Post-Data).

![alt text](/assets/BackupOptions1.png)

Then click the Backup button. Once completed a screen will show if it has or has not completed successfully. (Exit code 0 means it worked)

![alt text](/assets/BackupMessages.png)