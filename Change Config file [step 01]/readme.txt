1. Login to the router web interface under admin account

----------------------------------------------------------------------------------------------------------

2. Navigate to Settings → Configuration → Save to Computer
(if ask for password, enter a passwork and keep remind it)

----------------------------------------------------------------------------------------------------------

3. install latest Python Version

----------------------------------------------------------------------------------------------------------

4. Add Downloaded Configuration file from router and "cfgtool.py" file into same folder

Run the terminal in that folder and run this code line to Decode the configuration file
(cfgtool.py download link - https://github.com/r3d5ky/sercomm_cfg_unpacker)

"cfgtool.py -u configurationBackup.cfg"

----------------------------------------------------------------------------------------------------------

5. Open "configurationBackup.xml" and find the following line:

<PARAMETER name="Password" type="string" value="<your router serial is here>" writable="1" encryption="1" password="1"/>

----------------------------------------------------------------------------------------------------------

6. Insert the following line after and save:

<PARAMETER name="Enable" type="boolean" value="1" writable="1" encryption="0"/>

----------------------------------------------------------------------------------------------------------

Move out the "configurationBackup.cfg" file from the folder

----------------------------------------------------------------------------------------------------------

7. Encode the configuration:

"cfgtool.py -p configurationBackup.xml"

----------------------------------------------------------------------------------------------------------

8. Rename the new "configurationBackup_changed.cfg" to "configurationBackup.cfg"

----------------------------------------------------------------------------------------------------------

9. Upload the changed configuration file to the router
(if asking for a password, enter the password that you entered when download the "configurationBackup.cfg" file)

----------------------------------------------------------------------------------------------------------

10. Wait to automatically refresh the page and then login to the router web interface

username : SuperUser
password : ETxxxxxxxxxx
 
(where ETxxxxxxxxxx is the serial number from the backplate label)