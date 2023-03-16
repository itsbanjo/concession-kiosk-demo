
## Overview

## Purpose and Goal

## Intended Audience

## Network Diagram

## Requirements:

1. A RHEL8 VM template must be available for use before you can start provisioning the three tier application. 
2. The VM must contain open-vm-tools and perl for the VMware customization to successfully apply in the IPaddresses. 
3. The playbook also requires you to have an already existing A record for the frontend, backend, and mongodb. You can change these parameters related to networking by editing the inventory file to suit your needs. 
4. By default, the VM's are located in the /Testing folder of the in the VSphere. But thiscan be change at anytime by editing the *provision.yaml* 
5.  All three nodes must be reachable by the ansible controller node and are able to successfully intercommunicate. Its preferred that these VM's are in the same VLAN and a flat subnet for demo purposes to ensure a simple and successful deployment. 
6. create an ansible-vault named .majikmike in your working directory and should contain the following variables listed below. These are used for cloning a template and preconfiguring the network, hostname, and DNS name using open-vm-tools:
7. For demo purposes, you can create a .passwd file that contains the vault password for ease of use. However, you must delete these after demo.  


    vcenter_hostname:
    vcenter_username:  
    vcenter_password:  
    datacenter:  
    ansible_ssh_pass:  


8. community.vmware  collection is required

## Provisioning

To provision a VM using vmware_guest module:
*ansible-playbook provision.yaml --vault-password-file .passwd -i inventory *

To deploy the applications and MongoDB server:  
*ansible-playbook frontend.yaml --vault-password-file .passwd -i inventory  
ansible-playbook backend.yaml --vault-password-file .passwd -i inventory  
ansible-playbook mongodb.yaml --vault-password-file .passwd -i inventory*


## Test