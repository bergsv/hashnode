## How to Update a Rubrik CDM Cluster, a short rundown

This is just a short rundown how to upgrade an Rubrik CDM Cluster. This is also my first HowTo, when you want, you can give me feedback in the comments :)


1. First you have to check the compatibility matrix in the Rubrik Support Portal https://support.rubrik.com/s/article/000001571 (an eligible Support Portal Account is needed) 

2. Download the package from the Support Portal and note the corresponding MD5 Hash.

3. After the download is complete, compare the noted MD5 Hash with the MD5 Hash from the downloaded .zip-File. In Windows you can use the Command Prompt to generate the MD5 Hash for the .zip-File
```
C:\Users\MyUser\Downloads>certutil -hashfile rubrik-image-5.3.0-p3-18540.zip MD5
```
The output should look like this

 *MD5-Hash von rubrik-image-5.3.0-p3-18540.zip:
b030cb71aa684e881ea292bcdfc69060*

 Compare this output with the noted MD5 from the Support Portal

4. When both MD5 Hashes match, unzip the components of the .zip-File, not further. The Output will be an .tar.gz with a big size and an .tar.gz.sig with a very small size.

5. Connect via WinSCP to one Rubrik node (or the floating ip of your cluster, which is in reality, one node) and upload both files to the folder "/upgrade". The user for the login is "**adminstaging**" and the password comes from the standard admin-user.

6. Navigate to the Rubrik GUI, log in with administrative access and from the settings in the right top corner hit "Pause Protection". 

7. Check on the GUI if Live Mounts exists, when this is true, unmount or remove them. 

7. For the upgrade connect via SSH (Putty or Windows Command Prompt) to the node (or floating ip), you recently uploaded the update-image to. In this case you have to use the admin user and the corresponding password.

8. Run a pre-check with ```cluster upgrade start --mode prechecks_only``` and select the current upgrade package. After the pre-check is successful, begin with the upgrade, otherwise contact the Rubrik Support.

9. To start the update, type in ```cluster upgrade start```. When you got multiple packages, mostly old versions which are lying around the "/upgrade" folder, select the currently downloaded one.

10. You can watch the update status via ```cluster upgrade status```, to continously watch the update status, you can use ```cluster upgrade status watch```

11. The update may take a while, approximately 45 minutes for a four node cluster. The eleven update-states will be...
 * ACQUIRING
 * COPYING
 * VERIFYING
 * UNTARING
 * DEPLOYING
 * PRECHECKING
 * PREPARING
 * IMAGING
 * CONFIGURING
 * MIGRATING 
 * RESTARTING

 Your SSH Session will be disconnected multiple times, don't panic. You can always 
 reconnect to the node and watch the status with ```cluster upgrade status watch```

12. After the update finished successfully, delete the used tar.gz and tar.gz.sig files from the node you uploaded, connect to the Rubrik GUI and Resume the Protection from the settings menu. The update is done :)


