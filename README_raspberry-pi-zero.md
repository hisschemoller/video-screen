# Raspberry Pi Zero

* Raspberry Pi Zero W
* € 18,50
* https://www.kiwi-electronics.com/nl/raspberry-pi-zero-w-2852


![Connections](assets/images/raspberry-pi-zero-connections.jpg 'Connections')

## Setup

* *Raspberry Pi Imager* van https://www.raspberrypi.com/software/ installeren.
* Daarmee *Raspberry Pi OS Lite (Legacy)* op een micro SD kaart installeren.
* Copy a video file to the card (now called */boot*).
	* `wouter_hisschemoller_-_rembrandtplein_x8_-_2022.mp4`
* Create a file called ssh in */boot*: `touch ssh`. No content, this is a flag.
* Create another file in */boot*: `wpa_supplicant.conf` and fill in content:

```
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1
country=DE

network={
	ssid="Your WiFi network name"
	psk="Your WiFi network password"
	scan_ssid=1
	key_mgmt=WPA-PSK
}
```

* Enter SD card and connect power to the RPi.
* In a terminal connect with password 'raspberry' and run updates:

```
ssh pi@raspberrypi.local
sudo apt-get update -y
sudo apt-get upgrade -y
sudo apt dist-upgrade -y
```	

## VNC (Virtual Network Computing)

```
sudo raspi-config
```

* Select 'Interface Options'
* Select 'VNC'
* Yes, enable VNC Server
* Finish

Install VNC Viewer on the Macbook from https://www.realvnc.com/en/connect/download/viewer/

* Address: raspberrypi.local
* User: pi
* Password: raspberry

Maar in VNC Viewer krijg ik alleen maar 'Cannot Currently Show the Desktop'.

```
sudo raspi-config
# scherm resolutie naar 1280 x 960
```

Niks.

LXSession installeren (desktop environment) *(Werkt niet samen met omxplayer!)*

```
sudo apt-get install lxsession
```

Duurt lang. Was er waarschijnlijk nog niet.

Ja! Nu heb ik een desktop in VNC Viewer. Maar heb ik die eigenlijk nodig?


## Video playback

Copy a video file to `/boot`.<br>
`/boot/wouter_hisschemoller_-_rembrandtplein_x8_-_2022.mp4`

Install the libraries
```
ssh pi@raspberrypi.local
sudo apt-get update
sudo apt-get dist-upgrade
sudo apt-get install omxplayer
sudo apt-get install screen
```

Create a bash script
```
sudo nano /etc/init.d/videoloop
```

In nano, paste this code
```bash
#!/bin/bash
### BEGIN INIT INFO
# Provides: omxplayer
# Required-Start:
# Required-Stop:
# Default-Start: 2 3 4 5
# Default-Stop: 0 1 6
# Short-Description: Displays video file in a loop using omxplayer
# Description:
### END INIT INFO

# Video (replace with the path and file name of your video)
video_path=/boot/wouter_hisschemoller_-_rembrandtplein_x8_-_2022.mp4

#---- There should be no need to edit anything below this line ----

# Start displaying the video loop
case "$1" in start)
screen -dmS videoloop sh -c "omxplayer -o both $video_path -b --loop --no-osd"
echo "Video Playback Started"
;;

# Stop displaying video loop
stop)
sudo killall omxplayer.bin
echo "Video Playback Stopped"
;;

# Restart video loop if it died
repair)
if !(ps -a | grep omx -q)
    then
    screen -dmS videoloop sh -c "omxplayer -o local $video_path -b --loop --no-osd"
    echo "The Video is now running"
fi
;;

*)

echo "Usage: /etc/init.d/videoloop {start|stop|repair}"

exit 1

;;
esac
```

Ctrl + o to save and ctrl + x to exit nano.<br>
Change the file's permissions

```
sudo chmod 755 /etc/init.d/videoloop
```

Start the video loop
```
/etc/init.d/videoloop start
```

Stop the video loop
```
/etc/init.d/videoloop stop
```

Re-open the video loop if it closes:
```
/etc/init.d/videoloop repair
```

## Connect over USB (USB gadget mode)

Already done:

* Raspberry PI OS on SD card.
* Enable SSH: `touch ssh`.

In file */boot/config.txt* add a new line at the end:

```
dtoverlay=dwc2	
```

In file */boot/cmdline.txt* add after `rootwait` the text `modules-load=dwc2,g_ether` so the total
becomes:

```
console=serial0,115200 console=tty1 root=PARTUUID=a5c201d0-02 rootfstype=ext4 fsck.repair=yes rootwait modules-load=dwc2,g_ether
```

### To disable WIFI and it's settings

On the Macbook open *Users/[User]/ssh/known_hosts* and delete the `raspberrypi.local` entries.

Create an empty */boot/wpa_supplicant.conf* that will overwrite the previous one:

```
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1
country=DE
```

In file */boot/config.txt* add after `# /boot/overlays/README`:

```
dtoverlay=disable-wifi
dtoverlay=disable-bt # bluetooth as well, maybe not needed.
```

## Tutorials

* Play video in a loop
	* How to configure your Raspberry Pi Zero to play videos in a loop (= cheap and flexible solution for
video art installations)
		* https://alexasteinbruck.medium.com/how-to-configure-your-raspberry-pi-zero-to-play-videos-in-a-loop-cheap-and-flexible-solution-for-42f7744ed5c5
* VNC
	* Setting up a VNC screen share on a Raspberry Pi and a Mac
		* https://medium.com/@thedyslexiccoder/setting-up-a-vnc-screen-share-on-a-raspberry-pi-and-a-mac-10c1635df5dc
* Headless
	* Configure a headless Raspberry Pi from MacOS (No monitor or keyboard required)
		* https://micadep.medium.com/configure-a-headless-raspberry-pi-from-macos-no-monitor-or-keyboard-required-7afdfd067391
	* How to use a headless Raspberry Pi with VNC Connect
		* https://www.realvnc.com/en/blog/how-to-use-a-headless-raspberry-pi-with-vnc-connect/
	* How to set up VNC Connect on Raspberry Pi
		* https://www.realvnc.com/en/blog/how-to-setup-vnc-connect-raspberry-pi/
	* How to Set Up a Headless Raspberry Pi, Without Ever Attaching a Monitor
		* https://www.tomshardware.com/reviews/raspberry-pi-headless-setup-how-to,6028.html
* Connect via USB
	* How to run Raspberry Pi Zero by a micro-USB cable and a Mac
		* https://medium.com/@swiftycastle/how-to-run-raspberry-pi-zero-by-a-micro-usb-cable-and-a-mac-98a392059cc3
	* Connect to Your Raspberry Pi Over USB Using Gadget Mode
		* https://howchoo.com/pi/raspberry-pi-gadget-mode
* Connect via WIFI
	* How to setup multiple WiFi networks?
		* https://raspberrypi.stackexchange.com/questions/11631/how-to-setup-multiple-wifi-networks
* Disable WIFI
	* 7 Ways to Disable Wi-Fi on Raspberry Pi
		* https://raspberrytips.com/disable-wifi-raspberry-pi/
