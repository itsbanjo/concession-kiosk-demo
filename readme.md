
# Traditional to OpenShift



![image](https://user-images.githubusercontent.com/41275866/227836282-daf0b719-c997-4bfd-b84a-c69ceca73066.png)




## Purpose and Goal

The purpose of this repository is to showcase the transition of deploying an application, initially hosted on a traditional environment either a VMware or Virtualbox environmen then onto an OpenShift Local environment and finally to an OpenShift Platform. The goal is to provide an experiential comparison of the deployment process in terms of speed, consistency, and simplicity for containerized applications, utilizing the OpenShift Container Platform (OCP) versus a traditional deployment architecture. Specifically, we will deploy a three-tier application named "Concession Kiosk" on 3 RHEL instances of VMs through either VCenter or VirtualBox. Subsequently, we will deploy the same application on OpenShift using a few *oc* commands for comparative analysis.

In the typical Enterprise environment, deploying a three-tier application involves the coordination of multiple key stakeholders for production commissioning. However, in the Kubernetes world like OCP, the need for commissioning new infrastructure resources to meet specific requirements is considerably less common. Consequently, developers have the capability to consistently and securely manage deployments, responding faster to business needs. Simultaneously, platform administrators can concentrate on enhancing their Business-As-Usual (BAU) operations by incorporating additional capabilities into the platform. As OpenShift is managed using YAML, both developers and IT operations can collaborate effectively utilizing the latest technologies, preserving agility without compromise.

![The Sample Application](https://raw.githubusercontent.com/itsbanjo/concession-kiosk-demo/aae0e3838c9074da7986672a38455ae7a411e96f/artefacts/concession-kiosk.png)

Secondary objective is to display the power of Ansible, combined with HashiCorp's Vagrant for provisioning and configuration of system resources using a single YAML and Vagrantfile code.

## Intended Audience

Intended for beginners to intermediate in containerized applications and kubernetes deployment who are eager to have a hands-on experience the power of OpenShift.

## Network Diagram

## Requirements:
### Virtualbox deployment (Recommended)

1. ![Virtualbox](https://www.virtualbox.org/wiki/Downloads)
2. ![OpenShift Local](https://console.redhat.com/openshift/downloads)
3. ![OpenShift Client](https://console.redhat.com/openshift/downloads)
4. ![Vagrant](https://developer.hashicorp.com/vagrant/downloads)
5. ![Red Hat Account](https://www.redhat.com/wapps/ugc/register.html)

### VSphere Environment
1. ![OpenShift Local](https://console.redhat.com/openshift/downloads)
2. ![OpenShift Client](https://console.redhat.com/openshift/downloads)
3. (Optional) If you have a working Red Hat Satellite, you can use the ActivationKeys to register. Otherwise, the playbook will switch to manual subscription using your username and password. 
4. If you choose VSphere to deploy the VMs, a working account to successfully provision a VM is required. 
5. You **must** Create an ansible vault file named ***.majikmike*** before starting the provisioning process and should contain the following variables listed below:

```
       $ ansible-vault create .majikmike
       vcenter_hostname:
       vcenter_username:  
       vcenter_password:  
       datacenter:  
       ansible_ssh_pass:  
       rhsm_username: 
       rhsm_password: 
       rhsm_pool_id: 
```

## Deployment 
### VirtualBox: (Preferred, easiest and fastest)
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
   
    To skip auto-registering to Satellite, simply leave **bootstrap_foreman_fqdn** in **config.yaml** empty.
   
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
*NOTE:* alternatively, you can run ./deploy.sh located in the openshift folder
 ```
       $ oc new-project concession
       $ oc new-app https://github.com/itsbanjo/concession-kiosk-backend --name backend -e MONGODB_USER=concession -e MONGODB_PASSWORD=hello1234 -e DATABASE_SERVICE_NAME=mongodb -e MONGODB_DATABASE=concession  
       $ oc new-app https://github.com/itsbanjo/concession-kiosk-frontend --name frontend -e COMPONENT_BACKEND_HOST=backend -e COMPONENT_BACKEND_PORT=8080   
       $ oc import-image registry.access.redhat.com/rhscl/mongodb-36-rhel7 --confirm 
       $ oc process -f template.yaml -p MONGODB_USER=concession -p MONGODB_DATABASE=concession -p MONGODB_PASSWORD=hello1234 -p NAMESPACE=concession | oc apply -f - 
       $ oc expose svc frontend
       $ oc get routes  
```
Then`` browse the URL from the route results of the frontend above
