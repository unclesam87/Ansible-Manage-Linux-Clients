# Ansible-Manage-Linux-Clients
These playbooks are used to manage and setup linux clients (right now we are using fedora)

the wol-upgrade playbook is used to wake them up and update them - and when something changed to reboot them
for that we add to the inventory for every client an line like that
        client fqdn:
          macaddress: "00:00:00:00:00:00"
