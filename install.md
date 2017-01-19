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

<1> Run *ifconfig* in bash to see current IP address if necessary.

<2> You can config your network in CLI (**not suggest for beginners**).
Set up LAN (replace IP address in the command to the IP you want to use):
````bash
sudo ip addr add 192.168.1.111/24 dev eth0
````

Set up WLAN:
````bash
sudo ip addr add 192.168.1.112/24 dev wlan0
````

Set up route:
````bash
sudo ip route add default via 192.168.1.1
````

However connect to monitor and config it in GUI is much convnient (run *startx* in bash to start GUI).

####2.4.2 Start SSH service, stop GUI
Start the raspi-config GUI tool in the menu, *boot to CLI* and *enable SSH*.

(https://github.com/asdawn/acg/raw/master/images/config1-0-menu.PNG "raspi-config")

(https://github.com/asdawn/acg/raw/master/images/config1-1-cli.PNG "boot to CLI")

(https://github.com/asdawn/acg/raw/master/images/config1-2-ssh.PNG "enable SSH")

**TIPS**

<1> Why we use CLI?
Because GUI will cost *a lot* memory, network and CPU time, and later SSH and CLI is enough for us. We have to stop it to reserve resources for database and web service. Use *startx* when necessary.

<2> You can run *raspi-config* in bash to do the same thing.

2.4.3 Test SSH
The default user name is *pi*, and password is *raspberry*. You can use GUI tools or just run *ssh* to connect your PI:
````bash
ssh 192.168.1.112 -l pi
````
**TIPS**

Windows 10 can run Ubuntu (CLI only) in CMD. Enable * Windows Subsystem for Linux*, then run *bash* in CMD to install. Enjoy it.

## 3. Install tools for geo-processing
*GDAL, postgresql and PostGIS* can be installed directly using apt-get now. The *screen* tool is usefull when using SSH.
````bash
sudo apt-get update
sudo apt-get install postgresql postgis tomcat8 gdal-bin libgdal-java cmake libreadline-dev postgresql-server-dev-9.4 libboost-graph-dev libcgal-dev screen
````

The default *pgrouting* is a little out-of-date. So we download and compile the newest version. See also http://docs.pgrouting.org/2.3/en/doc/src/installation/install-dependencies.html. Some packages required are already installed in last step.

````bash
git clone git://github.com/pgRouting/pgrouting.git
cd pgrouting
mkdir build
cd build
cmake -L ..
make
sudo make install
````

**FAQs**

<1> Why can't I start psql?
postgres has a strict access control strategy, you can run it as user *postgres* and set up a pssword.

Start psql:

````bash
sudo su postgres
psql
````

Then alter the password:
````SQL
ALTER USER postgres WITH PASSWORD 'postgres';
\q
````
Here *\q* is the command to exit psql.

Finally log out (postgres):
````bash
exit
````

Now you can connect to postgresql:
````bash
psql -U postgres -h localhost
````

## 4. Start a RESTful service
## 5. Publish maps
## 6. Build a small cluster
## 7. Hadoop on PI









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
