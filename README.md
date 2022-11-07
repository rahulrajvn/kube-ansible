# kube-ansible
 
#### Pre-requisite
-  Ubuntu Nodes
-  1 Master Node and 2 Worker Nodes
-  Nodes Resolvable from local machine with below mentioned hostnames.

#### Following is the ansible hostfile with the Master and Worker node details. 
cat hosts 

[master]
kube-master  new_hostname=kube-master
[workers]
kube-worker1 new_hostname=kube-worker1
kube-worker2 new_hostname=kube-worker2
[all:vars]
ansible_user= {{ vm_user }}
ansible_sudo_pass= {{ vm_pass}}


#### Username and Passwords saved on the secret-vars.yml file.
cat secret-vars.yml 

vm_user: "XXXXXX"
vm_pass: "XXXXXXXXXX"


#### Checking the ansible can ping to all the nodes.
ansible all -m ping -o -e @secret-vars.yml


#### User Creation For Abisble Operations
ansible-playbook playbook/01_user_creation.yml -e @secret-vars.yml

#### Installtion of the Container Runtime ContainerD or Dokcer, We can use accordingly
ansible-playbook playbook/02_1_pkg_installs_containerd.yml -e @secret-vars.yml
ansible-playbook playbook/02_2_pkg_installs_docker.yml -e @secret-vars.yml

#### Initializing the Master Nodes and Configuring Ansible local node with Kube Config file. 
#Update the User path to which kubeconfig files needs to be copied. 
ansible-playbook playbook/03_master.yml -e @secret-vars.yml

#### Initializing the Worker Nodes
 ansible-playbook playbook/04_worker.yml -e @secret-vars.yml

#### Initialize the Metal-LB configuration with the latest config file
ansible-playbook playbook/05_metallb.yml -e @secret-vars.yml


#### Deploy a Sample Nginx Deployment 
ansible-playbook playbook/06_sample-nginx-deployment.yml -e @secret-vars.yml

#### Deploy a service to publish the deployed nginx through a load balancer. 
ansible-playbook playbook/07_sample-metallb-service-check.yml -e @secret-vars.yml






