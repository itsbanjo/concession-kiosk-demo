Requirements:

1. A RHEL8 VM template available for use. The VM must contain open-vm-tools and perl for the customization to work. The playbook also requires you to have an already existing A record for the frontend, backend, and mongodb. You can change these parameters related to networking by editing the inventory file to suit your needs. The provision playbook assumes too that the VM will placed inside the /Testing folder in VSphere. You may change this directly by editing the provision.yaml. All three nodes must be reachable by the ansible controller node and are able to successfully intercommunicate. Its preferred that these VM's are in the same VLAN and a flat subnet for demo purposes to ensure a simple and successful deployment. 

2. create an ansible-vault named .majikmike in your working directory and should contain the following variables listed below. These are used for cloning a template and preconfiguring the network, hostname, and DNS name using open-vm-tools:

3. For demo purposes, you can create a .passwd file that contains the vault password for ease of use. However, you must delete these after demo.  

`` 
vcenter_hostname: 
vcenter_username: 
vcenter_password:
datacenter: 
ansible_ssh_pass:  
``

4. community.vmware  collection is required

To provision a VM using vmware_guest module:
ansible-playbook provision.yaml --vault-password-file .passwd -i inventory 

To deploy the applications and MongoDB server:
ansible-playbook frontend.yaml --vault-password-file .passwd -i inventory 
ansible-playbook backend.yaml --vault-password-file .passwd -i inventory 
ansible-playbook mongodb.yaml --vault-password-file .passwd -i inventory 
