tl;dr version

get pi.
> sudo apt update
> sudo apt upgrade
> raspi-config [change password, config things as desired]
# install pihole
> sudo su [if you worry about pihole saying you're not superuser when running the next command]
> curl -sSL https://install.pi-hole.net | sudo bash
re static ip, choose what it suggests, or pick a higher number if the pi's not going to be on all the time, to avoid ip conflicts.
# point your router at the pihole
bathe in gloriously less visually noisy internets.

#### 20190827 by Red Tahr.


the non tl;dr version below (from a my gist)

written in the style of a markdown file, kinda, so # are titles, ## are subtitles etc.
run through as-is for a previously unpiholed network, if replacing a prior pi on the same IP 
then maybe setup pihole before doing the static ip.

write raspbian of your choice to micro SD (I'm using the lite version to keep needless processing down on the pi)

boot...first boot can take a minute if my memory serves me right.
connect somehow, if we're headless (my pi is plugged into the router) and you don't have a network scanner app lying about,
then start the IP address at one above your router and keep trying til you get asked to accept the fingerprint.
> ssh -l pi "ip address" 
(if ssh isn't on easiest headless option is to put an emtpy file names "ssh" in the root of the sd card and boot raspbian,
which will enable ssh for that session and raspbian will delete the ssh file itself.)

# change the default password (or skip to raspi-config and do that there)
> passwd

# do any raspbian housekeeping 
(turn on ssh by default, expand filesystem to use all of the SD card, set timezone, those sorts of things)
> raspi-config

# update raspbian........
> sudo apt update && apt upgrade
## but if the repo has changed its name/value then there might be some things to "accept explicitly"
> sudo apt update (then you'll be prompted to update the repo)
## if this times out or errors in some way, rerun it til it gets all the updates
> sudo apt upgrade
# optional(not advised without a good reason too) - update the firmware
> sudo rpi-update


# set a static ip address (pihole will offer to do this for you too if you prefer)
## this can change with OS version, so might need additional searching.
> sudo nano /etc/dhcpcd.conf (previously might have been /etc/network/interfaces)
uncomment the section that starts with # Example static IP configuration and set things how you want:
example:
# Example static IP configuration:
interface eth0
static ip_address=192.168.1.31/24
static ip6_address=0:0:0:0:0:ffff:c0a8:11f/64 (I used the ultratools url below to get the ip6 version of my chosen ip4 address)
static routers=192.168.1.1
static domain_name_servers=185.228.168.168 185.228.169.168 2a0d:2a00:2:: 2a0d:2a00:1::
(those DNS are the cleanbrowsing IPs, feel free to choose what you like.)
this can be changed to 127.0.0.1 after pihole is up and running.
### remember if replacing an old device on the same IP, make sure your router isn't looking to it for DNS, as that'll
be a nice lose of internet due to the pi looking at the router which looks back to the pi for DNS resolution :-)

# reboot raspbian and ssh into the new ip address
## if you already had a device at the new ip address, then the fingerprint might have changed, so windows will freak
## so edit or remove the known-hosts file
> rm ~\.ssh\known_hosts (if you know there's nothing in here you want to keep)

# pihole setup begins
## cloudflared (optional)
### get cloudflared
> cd ~
> wget https://bin.equinox.io/c/VdrWdbjqyF/cloudflared-stable-linux-arm.tgz
> mkdir argo-tunnel
> tar -xvzf cloudflared-stable-linux-arm.tgz -C ./argo-tunnel
> rm cloudflared-stable-linux-arm.tgz
> cd argo-tunnel

### install cloudflared (optional, and if you've installed raspbian-lite you'll likely need to install dnsmasq)
> sudo apt-get install screen
> screen
> sudo ./cloudflared proxy-dns --port 54 --upstream https://1.1.1.1/.well-known/dns-query --upstream https://1.0.0.1/.well-known/dns-query

(start another ssh connection if the pi is headless so you can continue)
## install pihole...
> curl -sSL https://install.pi-hole.net | sudo bash

yes to all the things, web interface, web server (so the web interface will exist), all third party lists...

### config pihole to use cloudflared (if installed)
> sudo nano /etc/dnsmasq.d/01-pihole.conf
changes are
#server=1.1.1.1
#server=1.0.0.1
server=127.0.0.1#54

> sudo nano /etc/pihole/setupVars.conf
changes are 
#PIHOLE_DNS_1=1.1.1.1
#PIHOLE_DNS_2=1.0.0.1

> sudo systemctl restart dnsmasq.service (the Pi-Hole will now send DNS requests to cloudflared which is running as our DoH proxy)

### run cloudflared as a service (if installed)
> sudo nano /etc/systemd/system/dnsproxy.service
copy:
[Unit]
Description=CloudFlare DNS over HTTPS Proxy
Wants=network-online.target
After=network.target network-online.target
 
[Service]
ExecStart=/home/pi/argo-tunnel/cloudflared proxy-dns --port 54 --upstream https://1.1.1.1/.well-known/dns-query --upstream https://1.0.0.1/.well-known/dns-query
Restart=on-abort
 
[Install]
WantedBy=multi-user.target

> sudo systemctl enable dnsproxy.service

# reboot to see if its all working...


# optional - install a firewall (ufw) and/or fail2ban
> sudo apt install ufw
## a basic ufw setup
> sudo ufw allow from 192.168.x.0/24 to any (allow your local network, change the ip to your local network)
> sudo ufw default deny incoming
> sudo ufw default allow outgoing
> sudo ufw allow 21/tcp
> sudo ufw allow 22/tcp
> sudo ufw allow 80/tcp
> sudo ufw allow 443/tcp
> sudo ufw enable [configure first before enabling, else you'll get locked out if running headless]

> sudo apt install fail2ban
> nano /etc/fail2ban/jail.local
[DEFAULT]
ignoreip = 127.0.0.0/24 192.168.1.0/24

### log2ram - so logs don't thrash the sd card soo much
> curl -Lo log2ram.tar.gz https://github.com/azlux/log2ram/archive/master.tar.gz
> tar xf log2ram.tar.gz
> cd log2ram-master
> chmod +x install.sh && sudo ./install.sh (was already executable, so chmod wasn't needed)
> cd ..
> rm -r log2ram-master (if you want to delete the repo...)

## pihole dark theme
https://github.com/lkd70/PiHole-Dark
> cd /var/www/html/
> sudo wget https://raw.githubusercontent.com/lkd70/PiHole-Dark/master/install.sh
> sudo chmod +x install.sh
> sudo ./install.sh

# references:
https://www.ultratools.com/tools/ipv4toipv6Result?address=192.168.1.31
https://www.raspberrypi.org/forums/viewtopic.php?p=1495033 (repo value change)
https://scotthelme.co.uk/securing-dns-across-all-of-my-devices-with-pihole-dns-over-https-1-1-1-1/
https://www.raspberrypi.org/documentation/configuration/security.md
https://support.opendns.com/hc/en-us/articles/228006487-FamilyShield-Router-Configuration-Instructions
http://hafenzoll.com/pages/setup-raspberry-pi-ufw-uncomplicated-firewall.html
http://www.heystephenwood.com/2013/06/setting-up-firewall-on-your-raspberry-pi.html
https://github.com/azlux/log2ram

https://cleanbrowsing.org/filters
https://hackernoon.com/dns-performance-comparison-google-quad9-opendns-norton-cleanbrowsing-and-yandex-d62d24e38f98

for something more detailed/thorough:
https://github.com/magicdude4eva/PiHoleCloudFlareD
