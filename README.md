# Smartthings-DSC-Alarm

This is a device handler that provides an on/off button to be used to arm/disarm your DSC Alarm system. It requires you to have already setup alarmserver per the instructions at https://github.com/juggie/AlarmServer/tree/smartthings. I set mine up on a Raspberry Pi. You can find a start script for alarmserver here https://gist.github.com/stevejenkins/3f5ea94ba603c9a28412. You then need to setup the SmartApp, Panel and Zone device handlers per the instructions here https://github.com/kholloway/smartthings-dsc-alarm. Once you have accomplished all of this then you can setup the on/off switch that I created.

First you need to edit the alarmserver to listen for HTTP traffic instead of HTTPS. Edit the alarmserver.py and search for HTTPChannel. There should be three entries. 

The second and third entries should look like this:

\#HTTPChannel(self, conn, addr)

HTTPChannel(self, ssl.wrap_socket(conn, server_side=True, certfile=config.CERTFILE, keyfile=config.KEYFILE, ssl_version=ssl.PROTOCOL_TLSv1), addr)

Change them to look like this:

HTTPChannel(self, conn, addr)

\#HTTPChannel(self, ssl.wrap_socket(conn, server_side=True, certfile=config.CERTFILE, keyfile=config.KEYFILE, ssl_version=ssl.PROTOCOL_TLSv1), addr)

Write and save the file and then restart the alarmserver service.

Next you need to add the DSC Alarm Button device handler to your Smartthings Device Types. In the Smartthings IDE under My Device Types click on New SmartDevice. Click From Code. Paste the code from DSCAlarmButton.groovy and click Create. Click Publish For Me. 
In Smartthings IDE under My Devices click New Device. Fill in the Name and Device Network Id fields with whatever you like. Select DSC Alarm Button in the Type drop down. Select the correct Location and Hub from the drop downs. Then click Create.
In Smartthings IDE under My Devices click the newly created device. Click edit, its next to Preferences. Enter the IP address of your alarmserver(the IP of the Raspberry Pi that is running alarmserver). The port should be 8111 unless you changed that to something else when you setup alarmserver. Click Save.

You should now have a working on/off switch for arming/disarming your DSC Alarm.
All network traffic for the arm/disarm stays on your local LAN. Meaning the smartthings HUB on your LAN sends the arm/disarm commands to the alarmserver and then the alarmserver passes those codes on to the DSC Alarm. All partition and zone related traffic, https://github.com/kholloway/smartthings-dsc-alarm, goes from the alarmserver to the Smartthings cloud via HTTPS.
