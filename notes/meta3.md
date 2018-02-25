# notes on building metasploitable 3 on macos
(just a summary of the steps for my quick reference)

install packer - download/install packer and add to path, https://www.packer.io/intro/getting-started/install.html,
or fastway via 
$ brew install packer

install vagrant, https://www.vagrantup.com/downloads.html
install plugin vagrant-reload, https://github.com/aidanns/vagrant-reload#installation, 
$ vagrant plugin install vagrant-reload

## follow instructions
https://github.com/rapid7/metasploitable3

clone repo

edit autounattend if adding license keys: https://github.com/rapid7/metasploitable3/wiki
