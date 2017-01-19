#### IMPORTANT: All changes happen at your own risk!
##### Anyone who is unsure should leave it alone, ask someone with experience to help or ask in the forum.
##### I am not responsible for damages that may incur from the use.

# CMU bringback

## Recovery libmc_user.so

### Problem
1. I can't login to the MZD FW56.00.513 by SSH (### Task1)
2. I don't have permission to modify the files. (### Task2)
3. Problem still exist (### Its another issue)

### Task1
To connect CMU. Choosing some method

#### Method 1: Enable WiFi AP Toggle

https://github.com/Siutsch/AIO---All-in-one-tweaks/blob/master/choose/docs/WIFI_AP_Toggle.txt

#### Method 2: Terminal Console

This method may not solve the problem because of USB issue.

<a href="http://www.youtube.com/watch?feature=player_embedded&v=M-iJLuxwfzU" target="_blank"><img src="http://img.youtube.com/vi/M-iJLuxwfzU/0.jpg" alt="JCI TEST" width="240" height="180" border="10" /></a>

#### Method 3: USB-to-Ethernet

This method may not solve the problem because of USB issue.
http://mazda3revolution.com/forums/1315810-post1650.html

#### Method 4: Serial Console

https://www.drive2.ru/l/452929522484904460/

### Task 2

Put shell code in `/data_persist/dev/bin/autorun`.



* Example (`/data_persist/dev/bin/autorun`)

```bash
#!/bin/sh
# /data_persist/dev/bin/autorun
sleep 40
if [ -e /mnt/sda1/run.sh ]
then
  sh /mnt/sda1/run.sh
fi
```

```bash
# typing command in car
# to allow execution
chmod +x /data_persist/dev/bin/autorun
```
Next time you can write command in `run.sh` without connect or modifed CMU.

Try command below for testing
```bash
#!/bin/sh
# dry run example : run.sh
/jci/tools/jci-dialog --title="Ready" --text="AUTORUN Activated" --ok-label='OK' --no-cancel
```

**Be very careful** to use below script.

```bash
#!/bin/sh
# SD Card : run.sh
PATH=$PATH:/bin:/sbin:/usr/bin:/usr/sbin
echo 1 > /sys/class/gpio/Watchdog\ Disable/value
mount -o rw,remount /
#Recommend to prevent Bootloop
sed -i 's/watchdog_enable=\"true\"/watchdog_enable=\"false\"/g' /jci/sm/sm.conf
sed -i 's|args=\"-u /jci/gui/index.html\"|args=\"-u /jci/gui/index.html --noWatchdogs\"|g' /jci/sm/sm.conf

#Put your command here
cp -a /mnt/sda1/libmc_user.so.511A-EU /jci/lib/libmc_user.so
mount -o ro,remount /
```

Then copy file from ([#Link Original libmc.so]( https://github.com/Siutsch/AIO---All-in-one-tweaks/tree/master/choose/config_org_all/media-order-patching/jci/lib)) to SDcard. Put it to car and reboot.

Next boot the CMU will execute command by root privillege.
