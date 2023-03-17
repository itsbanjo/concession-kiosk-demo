## Purpose and Goal

To showcase the speed, consistency, and simplicity of deploying a containerized applications using an OpenShift Container Platform (OCP) compared to deploying application using a traditional architecture. One three-tier application called "Concession Kiosk" is deployed using 3 RHEL instances of VMs via VCenter or VirtualBox. Then the same application is deployed into OpenShift invoking a few *oc* commands.

Typically in an Enterprise environment, deployment of a three-tier application requires several key stakeholders to comission into productions. Unlike in the Kubernetes world such as OCP, the comissioning new resources to meet the needs is uncommonly required. Hence, developer can take control the deployment consistently, securely, and faster response to the business needs. Both developers and IT operations can work more collaboratively using the latest technologies without compromising agility.

Secondary objective is to display the power of Ansible, combined with HashiCorp's Vagrant for provisioning and configuration of system resources using a single YAML and Vagrantfile code.

## Intended Audience

This is intended for beginners to intermediate in containerized applications and kubernetes who are eager to have a hands-on experience the power of OpenShift.

## Network Diagram

## Requirements:

1. A working access to access.redhat.com for registering the VM to Red Hat using the *Red Hat Developer Subscription for Individual*.
2. The pool ID for your *Red Hat Developer Subscription for Individual* that is required in *config.yaml* to successfully register the VM.
3. For VM's deployed in VMWare, a working account that is able to provision a VM is required. 
5. An optional configured Satellite to use Activation Keys. The playbook will switch to manual subscription method via access.redhat.com


### Traditional application deployment: 
1. To deploy the three tier application using Virtualbox, simply invoke *vagrant up* to provision the VMs and these will be assigned with the following IP's by default:   

| hostname  | ip address      |
|-----------|-----------------|
| Frontend  | 192.168.100.103 |
| backend   | 192.168.100.104 | 
| mongodb   | 192.168.100.105 | 

The playbook may attempt to connect to a different IP. Update the ***inventory*** accordingly. 

2. During the provisionining, it will ask you which interface to use.  Choose the one that is plugged in to your current network to create a bridge connection to your subnet.
   
3. Run *ansible-playbook -i inventory --vault-password-file .passwd all.yaml -u vagrant*

   
### VCenter deployment 


1. The configuration for the folder, VM template name, and network VLAN is located in configuration.yaml which is exclusive for provision.yaml
   
2. 3. To skip auto-registering to Satellite, simply leave bootstrap_foreman_fqdn in config.yaml empty.
   
3. A RHEL8 VM template must be available for use before you can start provisioning the three tier application. Ensure that VM template contains open-vm-tools and perl for the VMware customization to successfully apply the network customizations. 
   
4. All three nodes must be reachable by the ansible controller node and are able to successfully intercommunicate. Its preferred that these VM's are in the same VLAN and a flat subnet for demo purposes to ensure a simple and successful deployment. 
   
5. Create an ansible-vault named .majikmike in your working directory and should contain the following variables listed below. These are used for cloning a template and preconfiguring the network, hostname, and DNS name using open-vm-tools:

    vcenter_hostname:
    vcenter_username:  
    vcenter_password:  
    datacenter:  
    ansible_ssh_pass:  
    rhsm_username: 
    rhsm_password: 
    rhsm_pool_id: 

6. For demo purposes, you can create a .passwd file that contains the vault password for ease of use. However, you must delete these after demo.  

8. community.vmware  collection is required which required pyvmomi installed via pip
    ansible-galaxy install collection community.vmware
    pip3 install pyvmomi

## Provisioning

To provision a VM in VCenter: 
   $ ansible-playbook provision.yaml --vault-password-file .passwd -i inventory

To provisioning using VirtualBox
   $ vagrant up 

To deploy the applications and MongoDB server:  
   $ ansible-playbook all.yaml --vault-password-file .passwd -i inventory  
