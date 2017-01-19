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
ssh 192.168.1.102 -l pi
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

setenv.sh  LD¡£¡£¡£


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


