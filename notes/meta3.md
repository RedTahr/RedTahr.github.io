# my notes for me on building metasploitable 3, initially on macos
(just a summary of the steps for my quick reference)

install packer - download/install packer and add to path, https://www.packer.io/intro/getting-started/install.html,
or fastway via 
$ brew install packer
for windows via chocolatey (starting with installing chocolatey from an admin powershell)
Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
choco install packer

install vagrant, https://www.vagrantup.com/downloads.html
install plugin vagrant-reload, https://github.com/aidanns/vagrant-reload#installation, 
$ vagrant plugin install vagrant-reload

## follow instructions
https://github.com/rapid7/metasploitable3

clone repo

edit autounattend.xml if adding license keys: https://github.com/rapid7/metasploitable3/wiki
(ended up commenting out the valid key as kept getting told it wasn't valid, so will try it after installation has completed)

edit autounattend.xml if have downloaded iso already: https://github.com/joefitzgerald/packer-windows
