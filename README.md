# pi-kiosk


Start with [2018-11-13-raspbian-stretch-full](https://downloads.raspberrypi.org/raspbian_full_latest). Autologin for user `pi` is activated by default.


### Install packages
````bash
sudo apt-get install chromium-browser unclutter
````

### kiosk script

Use `nano /home/pi/kiosk.sh` to create the following script:

````bash
#!/bin/bash
export DISPLAY=:0

# Hide the mouse from the display
unclutter &

# If Chrome crashes (usually due to rebooting), clear the crash flag so we don't have the annoying warning bar
sed -i 's/"exited_cleanly":false/"exited_cleanly":true/' /home/pi/.config/chromium/Default/Preferences
sed -i 's/"exit_type":"Crashed"/"exit_type":"Normal"/' /home/pi/.config/chromium/Default/Preferences

# URL to be shown automatically
URL=https://www.youtube.com/embed/live_stream?channel=UC2jh1CcUo70B2Y7m9XaXCAQ&amp;autoplay=1;rel=0;fs=0;autohide=0;hd=0

# Start browser in kiosk mode
chromium-browser --kiosk --autoplay-policy=no-user-gesture-required $URL
````

Set executable bit for kiosk script:
````
chmod +x /home/pi/kiosk.sh
````

### Add kiosk script to autostart
````bash
mkdir -p /home/pi/.config/autostart
nano /home/pi/.config/autostart/kiosk.desktop
````

Add the following content:
````
[Desktop Entry]
Type=Application
Name=Kiosk
Exec=/home/pi/kiosk.sh
X-GNOME-Autostart-enabled=true
````

### Disable screen saver

Edit configuration file
````
sudo nano lightdm.conf
````

Uncomment line containing `xserver-command` and add the following parameter:
````
xserver-command=X -s 0 -dpms
````
