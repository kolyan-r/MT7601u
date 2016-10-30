ABSTRACT FOR ACCESS POINT�
      Hello guys. I�m Murat. First I want to say, my English is not good. This example will teach you for using unsupported Mediatek MT7601u based wireless chipsets in Access Point mode. So you can use this wireless antenna in your embedded or desktop based Linux robotic projects or sharing your internet connection(as like hotspot). No Hardware and Programming skills required. We will not use hostapd driver. Just follow above what I write.

Plug your Wi-Fi antenna to your PC or Raspberry Pi and write this command to check if you have Clone MT7601.
pi@raspberrypi:~ $ lsusb



---------------------------------RESIM------------------------------------


Look this image if Ralink ID is equal with your Wi-Fi dongle then everything is fine. Your module will work. Follow instractions for your linux based operating systems.

ON UBUNTU 32-64 BIT SYSTEMS�
On Ubuntu 16.04 32-64 bit LTS systems, you can compile this driver easily. But you must be first update your system with these commands.
sudo apt-get update
sudo apt-get upgrade -y

After if you dont have git package then you cant install  driver from git. Install with this command.
sudo apt-get install git -y


Get this driver with git clone method from this repo. Type to terminal

git clone https://github.com/muratdemirtas/MT7601u.git


Driver will download after this command to your home directory. Open downloaded driver folder with

cd /mt7601u

if you want to change your Access Point configuration use this command for opening.

If you have nano package then use

sudo nano  /home/pi/mt7601u/etc/Wireless/RT2870AP/RT2870AP.dat

If you dont have nano. Install from repository with command,

sudo apt-get install nano -y
write your settings and use save for configuration �ctrl-w� and press �y�


And compile your driver with this commands.

cp -v /lib/modules/$(uname -r)/build/Module.symvers src/os/linux
make clean && make all && make install
rm -rvf src/os/linux/Module.symvers

So you if not receive any error messages and success , driver is compiled and ready for Access Point mode. Make install command will deploy your Access Point drivers (rtnet7601Uap, mt7601Uap) to kernel automatically. But old MT7601 Chipset kernel module must be remove from Linux kernel system. Only and easy way fort his, add old mt7601 kernel mod�le to Linux blacklist. So you can this easy with these commands.

Just open your�/etc/modprobe.d/blacklist�file and add drivername using following syntax:

blacklist mt7601u 

EDIT: In later versions since 12.10 (12.04?) the file is�/etc/modprobe.d/blacklist.conf

Reboot your system and use lsmod command to show the status of modules in the Linux Kernel. If you dont see any mod�le as �mt7601u� then success. 


------------------------------------------RESIM RA0----------------------------------------------------------
Source: http://askubuntu.com/questions/110341/how-to-blacklist-kernel-modules
After reboot your system, Access Point is established and wait for dhcp install. 

-----------------------------------------------RESIM-------------------------------------------------------------
In now you can connect your Access Point but you can�t obtain IP adress because DHCP server isn�t installed. Install with these commands
sudo apt-get install hostapd isc-dhcp-server
(You may need to sudo apt-get update if the Pi can't seem to get to the apt-get repositories)

Next we will edit /etc/dhcp/dhcpd.conf, a file that sets up our DHCP server - this allows wifi connections to automatically get IP addresses, DNS, etc. Run this command to edit the file sudo nano /etc/dhcp/dhcpd.conf Find the lines that say option domain-name "example.org"; option domain-name-servers ns1.example.org, ns2.example.org; and change them to add a # in the beginning so they say #option domain-name "example.org"; #option domain-name-servers ns1.example.org, ns2.example.org; Find the lines that say # If this DHCP server is the official DHCP server for the local # network, the authoritative directive should be uncommented

and remove the # so it says # If this DHCP server is the official DHCP server for the local # network, the authoritative directive should be uncommented. authoritative;
Then scroll down to the bottom and add the following lines subnet 192.168.42.0 netmask 255.255.255.0 { range 192.168.42.10 192.168.42.50; option broadcast-address 192.168.42.255; option routers 192.168.42.1; default-lease-time 600; max-lease-time 7200; option domain-name "local"; option domain-name-servers 8.8.8.8, 8.8.4.4; }

Save the file by typing inControl-X then Y then return Run sudo nano /etc/default/isc-dhcp-server and scroll down to INTERFACES="" and update it to say INTERFACES="wlan0" Or whatever the name of your wifi adapter is!
Source: https://cdn-learn.adafruit.com/downloads/pdf/setting-up-a-raspberry-pi-as-a-wifi-access-point.pdf

set static IP for your wifi card type and route packages for internet share type these commands to terminal
sudo ifconfig ra0 192.168.42.1
sudo iptables -F
programmer@ubuntu:~$ sudo sh -c "echo 1 > /proc/sys/net/ipv4/ip_forward "
programmer@ubuntu:~$ sudo iptables -t nat -A POSTROUTING -o ens33 -j MASQUERADE
programmer@ubuntu:~$ sudo iptables -A FORWARD -i ens33 -o ra0 -m state --state RELATED,ESTABLISHED -j ACCEPT
programmer@ubuntu:~$ sudo iptables -A FORWARD -i ra0 -o ens33 -j ACCEPT
programmer@ubuntu:~$ sudo iptables -t nat -S

So your Access Point is ready
------------------------------------------RESIM




ON RASPBERRY PI EMBEDDED LINUX 32-64 BIT SYSTEMS�

For compiling for this module, use these commands for compiling and take a coffee.

1. pi@raspberrypi:~/Downloads/mi $ git clone https://github.com/eywalink/mt7601u.git
2. ....
3. pi@raspberrypi:~/Downloads/mi $ cd mt7601u/
4. pi@raspberrypi:~/Downloads/mi/mt7601u $ sudo ./miwifi_build.sh

1. Makefile:387: recipe for target 'LINUX' failed

1. sudo wget https://raw.githubusercontent.com/notro/rpi-source/master/rpi-source -O /usr/bin/rpi-source && sudo chmod +x /usr/bin/rpi-source && /usr/bin/rpi-source -q --tag-update
2. sudo rpi-source
3. i@raspberrypi:~ $sudo ifconfig wlan0 down
4. pi@raspberrypi:~ $sudo rmmod mt7601u
5. pi@raspberrypi:~ $sudo ifconfig ra0 up

---------------------------------------RESIM------------------------------------


















