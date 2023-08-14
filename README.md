# Ansible-Manage-Linux-Clients
These playbooks are used to manage and setup linux clients (right now we are using fedora)
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
## Setup Clients with Ventoy
we installed fedora on one client and then copyed the anaconda file and edited it:
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
