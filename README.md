# wireguard_ansible

This is the ansible automation of the Wireguard VPN set up described here https://www.ckn.io/blog/2017/11/14/wireguard-vpn-typical-setup/

This will create two VPN client profiles when done.

This project has also been restructured as an ansible role for inclusion in other ansible playbooks.




## Install ansible

```bash
sudo apt-add-repository ppa:ansible/ansible -y
sudo apt-get update && sudo apt-get install ansible -y
```
For optimal use, use ansible version greater than 2.5




Edit the inventory file in that folder and fill in the IP field with the VPN server IP

Begin the remote installation process by running

```bash
ansible-playbook wireguard.yml -i inventory
```



Client configs will be downloaded to the **/profiles** folder on your local host.


Copy client config to **/etc/wireguard/** and you can start using the VPN tunnel on your client.


To bring up/down the VPN interface
```bash
sudo wg-quick up wg0-client
sudo wg-quick down wg0-client
```

To view connection details
```bash
sudo wg show
```

# Advanced use

You have the option of determining the vpn network subnet you prefer your clients to use by editing the file **wireguard_role/defaults/main.yml**, and setting the vpn_network variable as desired. You can also change the vpn server port and the number of client profiles you want generated in the same file:


```bash 
vpn_network: '10.200.200'

vpn_port: '51820'

clients: 10
```

## Adding a client

If you want to generate an additional client profile in future, edit the following two variables in **wireguard_role/tasks/main.yml** to your specific needs:

```bash
    new_client: newclient
    new_client_ip: 10.200.200.12
```

Then run the setup process again but now with the tag **add_client** specified:

```bash
ansible-playbook wireguard.yml -u root -k -i hosts -t add_client
```

The new client config will then be downloaded to the **wireguard_role/profiles** folder on your local host.

Note: This needs to be run from the directory the initial setup was done from and not from a newly cloned one.

## Use as an ansible role

This project has been structured as an ansible role. You can therefore include it in other ansible playbooks

	- name: Setup Wireguard VPN
	  hosts: all
	  gather_facts: true
	  roles:
	    - {role: 'wireguard_role', tags: 'wireguard'}


# DNS

If there is another service listening on port 53, you will have issues with getting DNS resolution working.
It is therefore advisable to either disable or change the port of any service already using port 53. 
This will automatically be handled for you on Ubuntu 18.04 when you run this playbook.
