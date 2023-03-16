## Purpose and Goal

To showcase the speed, consistency, and simplicity of deploying a containerized applications using an OpenShift Container Platform (OCP) compared to deploying application using a traditional architecture. A three-tier application called "Concession Kiosk" is deployed using 3 VMs in a traditional fashion by invoking a few ansible commands listed below. Then a second type of application deployment using the OpenShift's *oc* command and deploying the same application. 

Typically in an Enterprise environment, deployment of a three-tier application requires several key stakeholders to comission the application to a prodution instances. Unlike in the Kubernetes world such as OCP, the comissioning resources to meet the needs is not required. Hence, developer can take control the deployment consistently, securely, and faster response to the business needs. Both developers and IT operations can work more collaboratively using the latest technologies without compromising agility.

Secondary objective is to display the power of Ansible, combined with HashiCorp's Vagrant for provisioning and configuration of system resources using a single YAML and Vagrantfile code.

## Intended Audience

This is intended for beginners to intermediate in containerized applications and kubernetes who are eager to have a hands-on experience the power of OpenShift.

## Network Diagram

## Requirements:

Traditional application deployment: 
1. A working VCenter (optional)
2. A VirtualBox


### VCenter deployment ###

1. A RHEL8 VM template must be available for use before you can start provisioning the three tier application. 
2. The VM must contain open-vm-tools and perl for the VMware customization to successfully apply in the IPaddresses. 
3. The playbook also requires you to have an already existing A record for the frontend, backend, and mongodb. You can change these parameters related to networking by editing the inventory file to suit your needs. 
4. By default, the VM's are located in the /Testing folder of the in the VSphere. But this can be change at anytime by editing the *provision.yaml* 
5.  All three nodes must be reachable by the ansible controller node and are able to successfully intercommunicate. Its preferred that these VM's are in the same VLAN and a flat subnet for demo purposes to ensure a simple and successful deployment. 
6. Create an ansible-vault named .majikmike in your working directory and should contain the following variables listed below. These are used for cloning a template and preconfiguring the network, hostname, and DNS name using open-vm-tools:
7. For demo purposes, you can create a .passwd file that contains the vault password for ease of use. However, you must delete these after demo.  


    vcenter_hostname:
    vcenter_username:  
    vcenter_password:  
    datacenter:  
    ansible_ssh_pass:  


8. community.vmware  collection is required

## Provisioning

To provision a VM in VCenter: 
*ansible-playbook provision.yaml --vault-password-file .passwd -i inventory *

To deploy the applications and MongoDB server:  
*ansible-playbook all.yaml --vault-password-file .passwd -i inventory  
