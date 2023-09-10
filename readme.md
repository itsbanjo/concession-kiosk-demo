
# Traditional to OpenShift



![image](https://user-images.githubusercontent.com/41275866/227836282-daf0b719-c997-4bfd-b84a-c69ceca73066.png)




## Purpose and Goal

A showcase the deployment transition of the same application from a traditional, OpenShift Local, to an OpenShift Platform. An experience to validate speed, consistency, and simplicity of deploying a containerized applications using an OpenShift Container Platform (OCP) compared to deploying application using a traditional architecture. A three-tier application called "Concession Kiosk" is deployed using 3 RHEL instances of VMs via VCenter or VirtualBox, then the same application is deployed into OpenShift invoking a few *oc* commands for comparison. 

Typically in an Enterprise environment, deployment of a three-tier application requires several key stakeholders to comission into productions. Unlike in the Kubernetes world such as OCP, the comissioning new infrastructure resources to meet the needs is uncommonly required. Hence, developer can take control the deployment consistently, securely, and faster response to the business needs. While Adminsitrator of the platform can focus on improving their BAU operations by adding additional new capabilities in the platform. Since OpenShift is managed using YAML, both developers and IT operations can work more collaboratively using the latest technologies without compromising agility.

![The Sample Application](https://raw.githubusercontent.com/itsbanjo/concession-kiosk-demo/aae0e3838c9074da7986672a38455ae7a411e96f/artefacts/concession-kiosk.png)

Secondary objective is to display the power of Ansible, combined with HashiCorp's Vagrant for provisioning and configuration of system resources using a single YAML and Vagrantfile code.

## Intended Audience

Intended for beginners to intermediate in containerized applications and kubernetes deployment who are eager to have a hands-on experience the power of OpenShift.

## Network Diagram

## Requirements:

1. A running OpenShift, (minimum OpenShift Local)
2. VirtualBox (recommended!)
3. A working access to access.redhat.com for registering the VM to Red Hat using the *Red Hat Developer Subscription for Individual*.
4. The pool ID for your *Red Hat Developer Subscription for Individual* maybe required in *config.yaml* to successfully register the VM if you want to register the hosts in a specific subscriptions. 
5. If you have a working Red Hat Satellite, you can use the ActivationKeys to register. Otherwise, the playbook will switch to manual subscription using your username and password. (Optional)
6. If you choose VSphere to deploy the VMs, a working account to successfully provision a VM is required. 
7. You **must** Create an ansible vault file named ***.majikmike*** before starting the provisioning process and should contain the following variables listed below:


       $ ansible-vault create .majikmike
       vcenter_hostname:
       vcenter_username:  
       vcenter_password:  
       datacenter:  
       ansible_ssh_pass:  
       rhsm_username: 
       rhsm_password: 
       rhsm_pool_id: 


## Deployment 
### VirtualBox: (Preferred and easiest)
1. To deploy the three tier application using Virtualbox, simply invoke *vagrant up* to provision the VMs and these will be assigned with a default IP:

       $ vagrant up    

| hostname  | ip address      |
|-----------|-----------------|
| Frontend  | 192.168.100.103 |
| backend   | 192.168.100.104 | 
| mongodb   | 192.168.100.105 | 

The playbook may attempt to connect to a different IP. Update the **VagrantFile** accordingly. 

1. During the provisionining, it will ask you which interface to use.  Choose the one that is plugged in to your current network to create a bridge connection to your subnet (see Network Diagram).
   
2. Run, ensure the **VagrantFile** and **inventory** file has the same IP addresses then invoke the command below to start the provisioning of the three tier application. It will roughly take 5 minutes to provision the entire system
3. 
   ```ansible-playbook -i inventory --vault-password-file .passwd all.yaml -u root```

   
### VCenter deployment 


1. The configuration for the folder, VM template name, and network VLAN is located in **configuration.yaml** which is exclusive for provision.yaml for the entire provisioning flow. 
   
2. 3. To skip auto-registering to Satellite, simply leave **bootstrap_foreman_fqdn** in **config.yaml** empty.
   
3. A RHEL8 VM template **must** be available for use before you can start provisioning the three tier application. Ensure that VM template contains **open-vm-tools** and **perl** for the VMware customization to successfully apply the network customizations via the VCenter API. 
   
4. All three nodes must be reachable by the ansible controller node and are able to successfully intercommunicate. Its preferred that these VM's are in the **same VLAN** and a flat subnet for demo purposes to ensure a simple and successful deployment. 
   
5. An ansible-vault named **.majikmike** in your working directory and should contain the following variables listed below. These are used for cloning a template and preconfiguring the network, hostname, and DNS name using open-vm-tools:

       vcenter_hostname:
       vcenter_username:  
       vcenter_password:  
       datacenter:  
       ansible_ssh_pass:  
       rhsm_username: 
       rhsm_password: 
       rhsm_pool_id: 

6. For demo purposes, you can create a .passwd file that contains the vault password for ease of use. However, you must delete these after demo.  

8. community.vmware  collection is **required** which required pyvmomi installed via pip   

       $ ansible-galaxy install collection community.vmware
       $ pip3 install pyvmomi

## Provisioning

To provision a VM in VCenter and deploy the application:

       $ git clone https://github.com/itsbanjo/concession-kiosk-demo.git && cd concession-kiosk-demo
       $ ansible-playbook provision.yaml --vault-password-file .passwd -i inventory
       $ ansible-playbook frontend.yaml --vault-password-file .passwd -i inventory  
       $ ansible-playbook backend.yaml --vault-password-file .passwd -i inventory  
       $ ansible-playbook mongodb.yaml --vault-password-file .passwd -i inventory  


## Testing the traditional deployment

Browse the frontend site using an IP or the fully qualified domain name (FQDN) that you've assigned for the frontend using port 8080 (e.g. *http://192.168.100.103:8080*)



## Deploying in OpenShift 

Clone the repo if you haven't then run the following commands in the openshift/ folder and this will provision the application immediately. Perform the same command for your other OpenShift clusters. 

       
       $ oc new-project concession
       $ oc new-app https://github.com/jankleinert/concession-kiosk-backend --name backend -e MONGODB_USER=concession -e MONGODB_PASSWORD=hello1234 -e DATABASE_SERVICE_NAME=mongodb -e MONGODB_DATABASE=concession  
       $ oc new-app https://github.com/jankleinert/concession-kiosk-frontend --name frontend -e COMPONENT_BACKEND_HOST=backend -e COMPONENT_BACKEND_PORT=8080   
       $ oc import-image registry.access.redhat.com/rhscl/mongodb-36-rhel7 --confirm 
       $ oc process -f template.yaml -p MONGODB_USER=concession -p MONGODB_DATABASE=concession -p MONGODB_PASSWORD=hello1234 -p NAMESPACE=concession | oc apply -f - 
       $ oc expose svc frontend
       $ oc get routes  

Then browse the URL from the route results of the frontend above
