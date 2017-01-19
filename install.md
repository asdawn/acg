# Geoprocessing using Raspberry PI 3
## 1. Todo list

+ Make it boot
+ Install tools for geo-processing
+ Start a RESTful service
+ Publish maps
+ Build a small cluster
+ Hadoop on PI

## 2. Make it boot
### 2.1 Hardware

>+ Raspberry PI 3
>+ A TF card (32GB or larger)  and a card reader
>+ A Monitor and a HDMI cable
>+ A mouse and a keyboard
>+ A 5V power adapter (at least 2A) and a micro-usb cable
>+ A computer

**TIPS**

<1> Other versions of Raspberry PI are also okay, but for some versions you have to use a SD card instead of a TF card. If you do not have one, the newest version is suggested.

<2> A TF card can be easily converted to SD card with an adapter (very cheap, sometimes as accessory of the card).

<3> Maybe your notebook or tablet has an integrated card reader.

<4> If your monitor do not have an HDMI slot, maybe you  can try your TV, or seek a convertor.

<5> Monitor, mouse and keyboard are required the first time you boot it.

<6> An USB 3.0 slot of your PC or notebook is okay in most cases. However it is not assured. Low power may lead to accidental halt or reboot.   

### 2.2 Software
>+ Image file of Rasbian (JESSIE)
>+ Image writer

Please read https://www.raspberrypi.org/documentation/installation/installing-images/README.md at the first time.

**WARNING**

Rasbian JESSIE LITE is a command line image. It is much smaller, but lacks some important packages (e.g. Java). Download the larger image (Jessie with PIXEL) to make it easy.

### 2.3 Write the image
Follow the steps in the hyper-link given in 2.2.

### 2.4 Start and config your PI
Plugin TF card, keyboard, mouse and monitor, then connect the power,  Raspberry PI will boot to GUI.
#### 2.4.1 Config your network
Find the network menu on the upper-right corner, right click to config IP addresses, left click to connect to WLAN. Later we mainly using SSH to access it, so a fixed IP address is needed.
![alt text] (https://github.com/asdawn/acg/raw/master/images/config-0-0-net.PNG "Wireless and wired network settings")

**TIPS**

## 3. Install tools for geo-processing
## 4. Publish maps
## 5. Build a small cluster
## 6. Hadoop on PI







    It is suggested to use the FULL image of rasbian, becaused the LITE version lack some necessary packages (e.g. jdk and jre).
    It is suggested to connect monitor, keyboard and mouse properly the first time you boot from the system image.
1. start ssh (you need a monitor at this time)
1.1 start ssh automatically

sudo raspi-config

advanced options->ssh->yes
1.2 close GUI to run quicker(optional)
sudo raspi-config
boot options->desktop/cli->console(or console auto-login)
1.3 config ip address
here 192.168.0.193 should be replaced with a valid ip you want to use
sudo ip addr add 192.168.0.193/24 dev wlan0
here 192.168.0.196 should be replaced with the router's ip
sudo ip route add default via 192.168.0.196

2. use ssh
````bash
ssh 192.168.1.102 -l pi
````
the default password is raspberry

3. install software ready for raspberry pi(arm)
fortunately now gdal, postgres, postgis and pgRouting can be installed using apt-get
sudo apt-get update
sudo apt-get install postgresql postgis tomcat8 libgdal-java cmake libreadline-dev  postgresql-server-dev-9.4 libboost-graph-dev libcgal-dev screen

http://docs.pgrouting.org/2.3/en/doc/src/installation/install-dependencies.html
yes, the default java is java 8
#here pgrouting we download from github
git clone git://github.com/pgRouting/pgrouting.git
cd pgrouting
mkdir build
cd build
cmake -L ..
make
sudo make install

4. change password of postgres (the default account of postgresql)
sudo su postgres
ALTER USER postgres WITH PASSWORD 'postgres';
\q

/usr/share/java/gdal.jar
:/usr/share/tomcat8/bin  tomcat bin
 /var/lib/tomcat8/webapps/ROOT/index.html tomcat webapps
http://192.168.1.109:8088/gis/format/convert?geometry=POINT(1 1)&trans=wkt2pts

setenv.sh  LD������


ssh 192.168.1.113 -l pi

How to shutdown without a monitor?
press ctrl+alt+F1 to get tty1,then enter
sudo shutdown now
wait a minute after the green LED is turned off, then it should be shutted down safely (in most cases).

How to run SSH in Windows 10?
Open a CMD windows, then enter
bash
maybe you have to retry several times. When the installation finished, you have an Ubuntu now (only CLI). It is similar to Rasbian, you can install SSH using
sudo apt-get install ssh

overheat
cat /sys/class/thermal/thermal_zone0/temp
53ocis dangerous
