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

<7> To check if it is overheated, run

````bash
cat /sys/class/thermal/thermal_zone0/temp
````

the return value*0.001 is the degree Celsius.

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

![alt text](https://github.com/asdawn/acg/raw/master/images/config1-0-menu.PNG "raspi-config")

![alt text](https://github.com/asdawn/acg/raw/master/images/config1-1-cli.PNG "boot to CLI")

![alt text](https://github.com/asdawn/acg/raw/master/images/config1-2-ssh.PNG "enable SSH")

**TIPS**

<1> Why we use CLI?
Because GUI will cost *a lot* memory, network and CPU time, and later SSH and CLI is enough for us. We have to stop it to reserve resources for database and web service. Use *startx* when necessary.

<2> You can run *raspi-config* in bash to do the same thing.

#### 2.4.3 Test SSH
The default user name is *pi*, and password is *raspberry*. You can use GUI tools or just run *ssh* to connect your PI:
````bash
ssh 192.168.1.112 -l pi
````
**TIPS**

<1> Windows 10 can run Ubuntu (CLI only) in CMD. Enable * Windows Subsystem for Linux*, then run *bash* in CMD to install. Enjoy it.

<2> How to shutdown without a monitor?
press ctrl+alt+F1 to get tty1,then enter

````bash
sudo shutdown now
````

wait a minute after the green LED is turned off, then it should be shutted down safely (in most cases).


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
We have already installed tomcat8. Suppose you have a ready to use .war file, how to deploy it? There are some tricks.
### 4.1 How to start/stop tomcat8
Start:
````bash
sudo service tomcat8 start
````  
stop:
````bash
sudo service tomcat8 stop
````

and restart:
````bash
sudo service tomcat8 restart
````

### 4.2 Important paths of tomcat8
#### 4.2.1 bin
````bash
/usr/share/tomcat8/bin
````

This folder contains tomcat8's start and stop scripts. However it is now a service, so we do not have to call these scripts manualy.

Besides, system enviroment variables can be configurated by create and edit a file named #setenv.sh# here.

#### 4.2.2 lib
````bash
/usr/share/tomcat8/lib
````

This should be the lib folder where to put .jar and .so files (there are two in total).

#### 4.2.3 webapps
````bash
/var/lib/tomcat8/webapps/
````

Put your .war file here after delete the ROOT folder and restart tomcat8.

The service is run by a user named *tomcat8*, so maybe you have to alter the access authority by using the command *chmod*. *chmod +777 filename* is a lazy but dangerous way, do not try it in important things.  You may try *sudo su tomcat8*.

#### 4.2.4 conf
````bash
/var/lib/tomcat8/conf/
````

*server.xml* is in this folder.

#### 4.2.5 logs
````bash
/var/lib/tomcat8/logs/
````

*catalina.out* in this folder logs console outputs. And there are other log files.

**TIPS**

To find files/directories of tomcat8, run
````
whereis tomcat8

dpkg -L tomcat8

````
And there are hints in the default webpage running on this server.

### 4.3 Debug for native libraries

Libraries in pure java should work well in tomcat. However some wrapped libraries or methods will fail. For example, when I deploy my war, GDAL can not work.

#### 4.3.1 Use the right versoin
Yes, I packaged a gdal.jar for X64 of version 2.0. PI is using 1.10 for ARM. So I copy it to *lib* (chmod and restarted tomcat8).

````
sudo cp /usr/share/java/gdal.jar /usr/share/tomcat8/lib/
sudo chmod +777 /usr/share/tomcat8/lib/gdal.jar
````

However, it still throws a link error.

#### 4.3.2 Add JNI path

It seems that tomcat can not find native libs. So I created a *setenv.sh* in bin (remember to *chmod* or *su*), the content is:

````
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/share/java/:/usr/lib/jni/:./
````

Restart tomcat, now GDAL is okay.

**TIPS**

List files of libgdal-java:
````bash
dpkg -L libgdal-java
````

These files are very important:

````bash
/usr/lib/jni/libgdalconstjni.so
/usr/lib/jni/libgdaljni.so
/usr/lib/jni/libogrjni.so
/usr/lib/jni/libosrjni.so
/usr/share/java/gdal.jar
````

### 4.4 Find the base direcotry

Now most services are okay, except the one using postgres. Connection parameters are written in a config file, but it seems tomcat did not find it.

This file's path is like "config.ini", on other servers put it into *bin* is enough. However, this time it do not works.
Someone's blog says, when start tomcat using *startup.sh*, the base directory is *bin*; however when run it as a service, the base directory is undefined.  I do not want to modify the program now, for it is running on several machines and the update is a headache. It's better to find another way.

Another blog says, the local (foreach war) base directory is  *WEB-INF/classes/*, I copy it to this folder, chmod and restart tomcat, failed. But I tried several other directories, finally found * /var/lib/tomcat8/* is a valid base directory. Yeah, all the REST services are usable on Raspberry PI now.

## 5. Publish maps

Here we use Geoserver.  Download it from http://geoserver.org/release/stable/, unzip and run it.

````bash
cd ~
wget http://sourceforge.net/projects/geoserver/files/GeoServer/2.10.1/geoserver-2.10.1-bin.zip
unzip -q geoserver-2.10.1-bin.zip
sudo mv geoserver-2.10.1 /usr/share/geoserver
sudo chown -R pi /usr/share/geoserver/
echo "export GEOSERVER_HOME=/usr/share/geoserver" >> ~/.profile
. ~/.profile
cd /usr/share/geoserver/bin
sh startup.sh
````

It failed, because port 8080 is used by tomcat8! So I changed the port of tomcat8 (edit *server.xml* in *conf*), and restarted it. Now the sample maps are published, shown in the layer preview pages.

It should be run as a service in the future, but now it is enough.

See also http://docs.geoserver.org/latest/en/user/installation/linux.html and http://docs.geoserver.org/latest/en/user/production/linuxscript.html#debian-ubuntu.
## 6. Build a small cluster
## 7. Hadoop on PI
