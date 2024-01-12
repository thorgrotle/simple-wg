# simple-wg is a Wireguard config file generator and manager
Created by Thor Grotle / keroit.dk

@ wireguard - https://www.wireguard.com/
@ qrencode - https://fukuchi.org/works/qrencode/

This application is divided into to parts, the server generator and the client generator

With wg-genserver, you can setup multiple instances(profiles), the application reads systemsettings and suggest default setting based on your system, however you can set them manually.
Each profile can have up to 250 clients configured currently, each client gets assigned a IP, which is defined via the clientcounter in the (profile).ini file.
You can create, view and delete profiles from wg-genserver

With wg-genclient, you can configure clients with QR code on each profile, the application reads the (profile).ini and creates client files from this.
A file will be created for the server profile, this contains the public key and IP for the client. 
The file can be found here: "/etc/wireguard/serverconf"

A second file will be created for the client, this is the will be displayed on the screen and a QR code for the client will be displayed together with the content of the client config.
The file can be found here: "/etc/wireguard/clientconf"

Initial setup:
-------------------------------- 
Software to install:
  wireguard-tools, qrencode

Enable Routing:
  $ sysctl -w "net.ipv4.ip_forward = 1"
For pernament add the following to /etc/sysctl.conf
  $ echo "net.ipv4.ip_forward = 1" >> /etc/sysctl.conf

Remeber to open firewall:
-------------------------------- 
For UFW (Ubuntu/Debian):
  $ ufw allow port 51820/udp

For firewalld (Redhat/Fedora):
  $ firewall-cmd --zone=public --add-port=51820/udp
  $ firewall-cmd --zone=public --add-port=51820/udp --permanent



wg-genserver help menu:
-------------------------------- 
 -h :  Displays this help screen
 -L :  List all Profiles
 -C :  Start the Profile creation
 -D :  Start the Profile deletion

 -p :  Defines profile
 -i :  Defines the Network interface wireguard profiles binds to
 -l :  Defines the port wireguards listenening on
 -e :  Define the Endport IP/hostname of the wireguard server
 -a :  Define the internal network address of the wireguard network
 -d :  Define DNS server for wireguard client
 -s :  Define secondary DNS server for wireguard client

Example:   wg-genserver -C -p business-network -i eth0
Example2: Create where you will be asked for all parameters:   wg-genserver -C

You can bring up/down wireguard profile with wg-quick
  wg-quick up business-network
  wg-quick down business-network

Check curernt status of wireguard:
 wg # shows all wireguard profiles
 wg show business-network

 
You can have wireguard automaticly bring up wireguard using systemd with the following command:
  systemctl enable wg-quick@business-network
  systemctl disable wg-quick@business-network


wg-genclient
-------------------------------- 
 -h :  Displays this help screen
 -L :  View profiles and clients
 -C :  Start the client creation
 -D :  Start client deletion

 -p :  Defines profile
 -c :  Defines client

Example: wg-genclient -C -p business-network -c Client1
Example2: Delete a client from profile: wg-genclient -D -p business-network -c Client1



File structure and layout:
----------------------------
/usr/local/bin/wg-genserver
/usr/local/bin/wg-genclient
/etc/wireguard/(profile).conf # <- This is the main wireguard configuration for each instance/profile.
/etc/wireguard/wgaddclients-(profile) <- this is the executable responsible for importing clients, one is created for each profile
/etc/wireguard/profile/(profile).ini <- This is the ini and cconfiguration file for simple-wg.
/etc/wireguard/clientconf/(profile)/(clientprofile).conf <- this is the file you give to your client/peer.
/etc/wireguard/serverconf/(profile/(clientprofile-serveer).conf <- this contains config file the server/profilee loads for each client
/etc/wireguard/keypair <- tempfolder for key generation
