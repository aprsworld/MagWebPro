# Files
* addDataPartition 

   Use sfdisk (from linux-utils) to create data partition that fills SD card. Then mount it.

* aprsStart

   System startup script that sets clock, sets hostname, generates network configuration, creates data partition if needed, LCD messages, firewall

* aprsStartDataGS

   Starts DataGS

* eepromSetHostname

   Sets hostname of system from EEPROM

* initialTestSetup

   Initial factory setup of device

* magnumReaderStart

   Start magnum reader data source

* networkFromJSON

   Creates /etc/network/interfaces file from network config JSON (see aNetConf)

* purgeHistory

   Remove history and development settings prior to shipping

* purgeLogLocal

   Remove logged data prior to shipping

* root-ro

   Set root filesystem to read-only mode

* root-rw

   Set root filesystem to read-write mode

* sethostname

   Set system hostname. Change running hostname. Store hostname to EEPROM

* wpaSupplicantScan

   Interact with wpa_supplicant to scan for available networks. Cannot be used with running as an access point.

# MagWebPro

This documents covers the software that makes up the MagWebPro and derivitive devices. The MagWebPro software stack runs on Raspberry PI 2 hardware.

## Raspbian

Based on Raspbian Jessie Lite from 2017-01-11.

Made to be read only and have basic configuration with the following:

* Packages
```
apt-get remove --purge wolfram-engine triggerhappy anacron logrotate dphys-swapfile xserver-common lightdm
insserv -r x11-common; apt-get autoremove --purge
apt-get install busybox-syslogd; dpkg --purge rsyslog

```

* Adding `ro` flag to /boot/cmdline

* Removing some directories under `/var`
```
rm -rf /var/lib/dhcp/ /var/run /var/spool /var/lock /etc/resolv.conf
```

* Relocating those directories to run on a tmpfs location
```
ln -s /tmp /var/lib/dhcp
ln -s /tmp /var/run
ln -s /tmp /var/spool
ln -s /tmp /var/lock
```
* Modifying `/lib/systemd/system/systemd-random-seed.service` to have it create random seed in a writeable location

* Disabling some services
```
insserv -r bootlogs; insserv -r console-setup
```

* Adding ro flags to / and /boot in fstab
* Adding tmpfs /tmp to fstab
* Setting hardware options in /boot/config.txt
* disabling RPI 3 on-board WiFi and Bluetooth using /boot/config.txt and blacklisting modules and
```
systemctl disable bluetooth.service
systemctl disable hciuart.service
```
* disabling some other un-needed services
```
systemctl disable systemd-tmpfiles-clean
systemctl disable avahi-daemon

```


## overall structure of MagWebPro

* basic system

Requires libi2c-dev
```
apt-get install libi2c-dev
```

* (APRS World) DataGS
Requires java
```
apt-get install oracle-java8-jdk
```

* (APRS World) tinyFrontPanelLcd

Splash screen put on screen using `basic.target` systemd service 

Configuration program on screen once network is up

Requires a webserver capable of running PHP. We are using Monkey web server and php5-cgi

* (APRS World) piNetConfig

* (APRS World) i2c utilties

* (APRS World) magnumReader
