# Penetration Testing and Ethical Hacking with Kali Linux



## kali_update.sh
nano kali_update.sh
apt-get update && apt-get upgrade -y && apt-get dist-upgrade -y
chmod +x kali_update.sh

### list installed packages
dpkg -l
dpkb -l ¦ grep zip

### sources file
nano /etc/apt/sources.list