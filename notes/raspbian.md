#my notes on setting up a raspbian image from clean (2017, debian-stretch)
new img, passwd to update password from default (raspberry).

# https://www.modmypi.com/blog/how-to-give-your-raspberry-pi-a-static-ip-address-update
> sudo nano /etc/dhcpcd.conf
at the bottom add/update your preferred network...
"You’ll need to edit the numbers in the snippet so they match your network configuration.

CODE

interface eth0

static ip_address=192.168.0.10/24
static routers=192.168.0.1
static domain_name_servers=192.168.0.1

interface wlan0

static ip_address=192.168.0.200/24
static routers=192.168.0.1
static domain_name_servers=192.168.0.1
 

interface = This defines which network interface you are setting the configuration for.
static ip_address = This is the IP address that you want to set your device to. (Make sure you leave the /24 at the end)
static routers = This is the IP address of your gateway (probably the IP address or your router)
static domain_name_servers = This is the IP address of your DNS (probably the IP address of your router). You can add multiple IP addresses here separated with a single space."

then
> reboot
and to check things
> ifconfig