# Raspberry Pi Zero

* Raspberry Pi Zero W
* € 18,50
* https://www.kiwi-electronics.com/nl/raspberry-pi-zero-w-2852


## Setup

* *Raspberry Pi Imager* van https://www.raspberrypi.com/software/ installeren.
* Daarmee *Raspberry Pi OS Lite (Legacy)* op een micro SD kaart installeren.
* Copy a video file to the card (now called */boot*).
* Create a file called ssh in */boot*: `touch ssh`. No content, this is a flag.
* Create a file called ssh in */boot*: `wpa_supplicant.conf` and fill in content:

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

### VNC screen share




## Tutorials

### Play video in a loop

How to configure your Raspberry Pi Zero to play videos in a loop (= cheap and flexible solution for
video art installations)<br>
https://alexasteinbruck.medium.com/how-to-configure-your-raspberry-pi-zero-to-play-videos-in-a-loop-cheap-and-flexible-solution-for-42f7744ed5c5<br>

### VNC

Setting up a VNC screen share on a Raspberry Pi and a Mac<br>
https://medium.com/@thedyslexiccoder/setting-up-a-vnc-screen-share-on-a-raspberry-pi-and-a-mac-10c1635df5dc<br>

### Headless

Configure a headless Raspberry Pi from MacOS (No monitor or keyboard required)<br>
https://micadep.medium.com/configure-a-headless-raspberry-pi-from-macos-no-monitor-or-keyboard-required-7afdfd067391<br>
