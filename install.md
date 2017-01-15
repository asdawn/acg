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
sudo apt-get install postgis
4.
