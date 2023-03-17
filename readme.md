## Purpose and Goal

To showcase the speed, consistency, and simplicity of deploying a containerized applications using an OpenShift Container Platform (OCP) compared to deploying application using a traditional architecture. One three-tier application called "Concession Kiosk" is deployed using 3 RHEL instances of VMs via VCenter or VirtualBox. Then the same application is deployed into OpenShift invoking a few *oc* commands.

Typically in an Enterprise environment, deployment of a three-tier application requires several key stakeholders to comission into productions. Unlike in the Kubernetes world such as OCP, the comissioning new resources to meet the needs is uncommonly required. Hence, developer can take control the deployment consistently, securely, and faster response to the business needs. Both developers and IT operations can work more collaboratively using the latest technologies without compromising agility.

Secondary objective is to display the power of Ansible, combined with HashiCorp's Vagrant for provisioning and configuration of system resources using a single YAML and Vagrantfile code.

## Intended Audience

This is intended for beginners to intermediate in containerized applications and kubernetes who are eager to have a hands-on experience the power of OpenShift.

## Network Diagram

## Requirements:

1. A working access to access.redhat.com for registering the VM to Red Hat using the *Red Hat Developer Subscription for Individual*.
2. The pool ID for your *Red Hat Developer Subscription for Individual* that will be later used in a YAML file.
3. For VM's deployed in VMWare, a working account that is able to provision a VM is required. 
5. An optional running and configured Satellite to use Activation Keys. The playbook will switch to manual subscription method via access.redhat.com


### Traditional application deployment: 
1. To deploy the three tier application using Virtualbox, simply invoke *vagrant up* to provision the VMs.
2. Run *ansible-playbook -i inventory --vault-password-file .passwd all.yaml -u root*
   


### VCenter deployment 

1. A RHEL8 VM template must be available for use before you can start provisioning the three tier application. 
2. The VM must contain open-vm-tools and perl for the VMware customization to successfully apply in the IP addresses. 
3. 
4. By default, the VM's are located in the /Testing folder of the in the VSphere. But this can be change at anytime by editing the *provision.yaml* 
5.  All three nodes must be reachable by the ansible controller node and are able to successfully intercommunicate. Its preferred that these VM's are in the same VLAN and a flat subnet for demo purposes to ensure a simple and successful deployment. 
6. Create an ansible-vault named .majikmike in your working directory and should contain the following variables listed below. These are used for cloning a template and preconfiguring the network, hostname, and DNS name using open-vm-tools:
7. For demo purposes, you can create a .passwd file that contains the vault password for ease of use. However, you must delete these after demo.  


    vcenter_hostname:
    vcenter_username:  
    vcenter_password:  
    datacenter:  
    ansible_ssh_pass:  
    rhsm_username: 
    rhsm_password: 
    rhsm_pool_id: 



8. community.vmware  collection is required

## Provisioning

To provision a VM in VCenter: 
*ansible-playbook provision.yaml --vault-password-file .passwd -i inventory *

To deploy the applications and MongoDB server:  
*ansible-playbook all.yaml --vault-password-file .passwd -i inventory  
