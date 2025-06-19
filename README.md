# Ansiguard

* Ansiguard is a configuration project based on **Ansible** that sets up a secure **WireGuard VPN server** and provides an easy way to generate client configuration files.
* It simplifies VPN provisioning with minimal manual steps, modular architecture, and bare-metal deployment support.

---

## What it does

* Automates full WireGuard server setup via Ansible
* Modular and easy-to-extend Ansible role structure
* Generates peer (client) configuration files
* Supports custom network ranges, ports, and keys
* Ensures secure configuration
* Works with cloud VMs and bare-metal

---



## How it works

* The server setup playbook (`setup_server.yaml`) installs **WireGuard**, configures the server interface, sets up forwarding rules, and enables the VPN service.
* The client generator playbook (`add_peer.yaml`) creates new peer keys, configuration files, and optionally QR codes for mobile clients.
* All key files and configs are stored on the remote server / virtual machine under /etc/wireguard.

---



## Prerequisites

* Ansible 2.10+
* A Linux server (tested on Ubuntu 20.04/22.04 and Debian)
* sudo privileges on the target host
* Python 3 installed on the target machine

---

## Quick start

```bash
# Clone and enter project
git clone https://github.com/festimb/ansiguard.git
cd ansiguard


# Replace your inventory with your host config (inventory.yaml)
all:
  hosts:
    my_server:
      ansible_host: <your.server.ip>
      ansible_port: <server_ssh_port>
      ansible_user: <your_server_user>
      ansible_password: <your_user_password>


# Run the Server playbook
ansible-playbook -i inventory.yaml playbooks/setup_server.yaml -e "user_name=festim user_password=securepassword" -K
# -> This requires you to specify the username and password in server that will be used to setup, can be same as inventory.
#This will: install WireGuard -> create server keys -> configure interface -> start and enable the Wireguard service.


# Generate a client config (repeat as many times as needed)
ansible-playbook -i inventory.yaml playbooks/add_peer.yaml -e "client_name=festim peer_allowed_ips=10.0.0.2/32 server_ip=192.168.20.1" -K
# -> This will require you to specify: the client name (can be anything), peer_allowed_ips (client's ip on server, must be unique), and IP of server
#This will: create a peer keypair -> Add the peer to the server config -> Generate festim.conf


# If you wish to further optimise the networking and default configuration, you may edit these two files with your desired configs:
- roles/peer/templates/peer.conf.j2
- roles/server/templates/server.conf.j2
```



