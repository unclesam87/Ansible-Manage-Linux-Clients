# Ansible Playbooks to manange linux desktop clients
These playbooks are used to manage and setup linux clients (right now we are using fedora)
- cinnamon desktop
- create an guest user and hides other users (in this example sysinst) from login screen
  - right now we dont use xguest because it doesnt work with cinnamon
  - that guest user deletes everything when rebooted or logged out via logon and logoff script
- add ricoh printer with usercode support
  - adds an small script on the desktop that allows u to enter ur usercode and saves them in that user session
- installs several programs

## Setup Clients with Ventoy
we installed fedora on one client and then copyed the anaconda file and edited it:
> all choices made during the installation are saved into a file named anaconda-ks.cfg, located in the /root/
https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/installation_guide/sect-kickstart-howto

Adding this line and using it with ventoy allows us to name that computer at setup.
```
# Network information
network  --hostname=$$PCNAME$$
```
SSH Key for Ansible
```
# SSH Key
sshkey --username=username "key"

# Firewall Rules
firewall --enabled --ssh
```
for ventoy auto install plugin look at this:
> https://www.ventoy.net/en/plugin_autoinstall.html

## Install Ricoh Printer
as we needed an method to install ricoh printer with lpoptions (so we would be able to set up addtional options), we came up with that solution:

```
    - name: check which printer are installed 
      shell: 
        cmd: "lpstat -t"
      ignore_errors: true
      register: cups_printers
    - name: if printer "Printername"" is not installed
      shell: 
        cmd: "lpadmin -p Printername -E -v socket://ip:9100 -P /opt/share/ppd/PPD.ppd -o finisher=FinAMURBBK -o print-color-mode=monochrome -o printer-is-shared=false -o OptionTray=LCT -o Duplex=None -o UserCode=Custom.STRING -o PageSize=A4"
      when: "'Printername' not in cups_printers.stdout"
```
First we check which printers are installed - when the printer with "Printername" is missing, we add it (see the lines with "register: cups_printers" and "when: "'Printername' not in cups_printers.stdout"

## Wake on LAN and Update
the wol-upgrade playbook is used to wake them up and update them - and when something changed to reboot them
for that we add to the inventory for every client an line like that
```
all:
  hosts:
  children:
    clients:
      hosts:
        client fqdn:
          macaddress: "00:00:00:00:00:00"
```
## ToDos
- [ ] use xguest because safer
- [ ] log out user when inactive for an certain time
- [ ] clean up the programm installation into an list that can easly edited
- [ ] clean up printer install into an role
