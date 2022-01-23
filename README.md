# WLANPi Hotspot

WLAN Pi Hotspot mode for the RPi/CM4 based platforms

(__Note: In this initial version, only 2.4GHz operation is supported due to platform support issues - this is under investigation__)

*Turn your WLANPi in to test a AP*

The WLAN Pi hotspot mode has been created to provide a quick and dirty wireless AP for tasks such as wall attenuation measurements. It will also allow a temporary wireless connection when you'd like to hook up to a switch ethernet port and extend the network connection out to a wireless client. Finally, it can be used to provide wireless access to the WLAN Pi itself if an OTG or Ethernet connection is not available.

**Background**: This package was created using the information in François Vergès blog article : [WLAN Pi - Setup a Wi-Fi Hotspot](https://www.semfionetworks.com/blog/wlan-pi-setup-a-wi-fi-hotspot)

## Quickstart

Although this document provides comprehensive instructions around the configuration and use of hotspot mode, you may want to quickly configure hostspot mode without reading lot of documentation. In the current release. we have provided a "quickstart" script to speed things up.

With the WLAN Pi in classic mode, SSH to the WLAN Pi and execute the command:

```
sudo quickstart-hotspot
```

Follow the on-screen instructions to configure hotspot and switch in to hotspot mode. If you need a deeper dive in to hotspot, please see the instructiosn provided in the remainder of this document.

## Configurations Options

It is very likely that you will not want to use this utility with the default shared key, channel and SSID. The defaults are:

* shared key: wifipros
* ssid: wlanpi_hotspot
* channel: 6

To change from default settings, ensure that the WLANPi is operating in standard "classic"mode (i.e. it has not already been switched in to another node such as wireless console mode). Then, edit the file: /etc/wlanpi-hotspot/conf/hostapd.conf. This can be done by opening an SSH session to the WLANPi and using the 'nano' editor:

```
 sudo nano /etc/wlanpi-hotspot/conf/hostapd.conf
```

There are numerous fields you can configure to change the behavior of the hotspot access point feature, but here are some of the more likely fields you'll want to look at and perhaps update (note that lines beginning with a # character are comments and do not affect operation):

```
    # WLAN SSID
    ssid=wlanpi_hotspot

    # WPA-PSK
    wpa_passphrase=wifipros

    # Mode options: a=5GHz / g=2.4GHz
    hw_mode=g

    # Set 2.4GHz Channel - 1,6,11
    # Set 5GHz Channel - 36,40,44,48,149,153,157,161,165
    channel=6

    # Set Country Code (Use your own country code here)
    country_code=CA
```

Once you have made your changes, hit Ctrl-X in the nano editor to exit and hit "Y" to save the changes when prompted.

Next, flip the WLANPi in to "Hotspot" mode using the front panel controls of the WLAN Pi. The required front panel option is: "Menu > Modes > HotSpot > Confirm". This will trigger a reboot of the WPAN Pi to switch it into to hotpot mode.  After the accompanying reboot, the WLANPi should operate using the parameters configured in hostapd.conf.

If you need to make any subsequent alterations to the parameters configured in hostapd.conf, after making your edits, reboot the WLAN Pi so that they take effect.

# Using Hotspot Mode

Following the WLANPi reboot, your configured hotspot SSID should be available in the network list shown on your wireless client (e.g. laptop).

Once you have joined the SSID, an IP address is assigned to your client device via DHCP and you will have access to the WLAN Pi itself (e.g. SSH to 172.16.43.1). You will be able to access features such as the built-in speedtest utility using a browser pointed at : http://172.16.43.1/

If the Ethernet port of the WLAN Pi is connected to a switch port that can provide an IP address to the WAN Pi, traffic can be forwarded (routed) from your wireless client and out to the Ethernet port. This can be useful to provide an extended network connection from an existing wired network.

# Switching to Hotspot Mode From CLI

It is possible to flip in to Wi-Fi hotspot mode using the Linux CLI, but it is recommended to use the WLAN Pi front panel navigation menu if you're not comfortable using the Linux CLI.

As there are quite a few networking changes we need to make for the Hotspot to operate correctly, we need to flip the WLANPi in to a completely new mode of operation that uses a different network configuration. The 'hotspot_switcher' script is used to switch between the usual "classic" mode of operation and the "Hotspot" mode. 

When moving between modes, the WLANPi will reboot to ensure that all new network configuration starts cleanly. 

### Enabling Hotspot Mode (Via CLI)

To flip the WLANPi in to "Hotspot" mode, SSH to the WLANPi and execute the following command:

```
 sudo hotspot_switcher on
```

At this point, the WLANPi will reboot so that the new networking configuration will take effect. 


### Exiting Hotspot Mode (via CLI)

To switch out of "Hotspot" mode, SSH to the WLANPi using network address 172.16.43.1 and run the command: 

```
 sudo hotspot_switcher off
```

When this command is executed, the original ("classic" mode) networking configuration files will be restored and the WLANPi will reboot. After the reboot, the WLANPi will operate as it did before the switch to "Hotspot" mode.
