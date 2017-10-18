LXD Container Lab
=================

```
# prepare container
lxc launch ubuntu:16.04 scalr
lxc exec scalr -- bash
# install in container
su - ubuntu
sudo apt update
sudo apt install python-pip
sudo pip install --upgrade pip
sudo pip install --upgrade pyOpenSSL
sudo pip install ansible==2.4
# clone repo with ansible role
git clone https://github.com/eumel8/ansible-scalr-vm-importer
# go to directory
cd ansible-scalr-vm-importer
# adjust credentials for scalr and openstack
vi credentials.yml 
# install and configure clients
ansible-playbook -e@credentials.yml scalr.yml --tags installation,configuration
# test openstack client
openstack --os-cloud default server list
# test scalr client
scalr-ctl farms list --tree
# import images
ansible-playbook  scalr.yml --tags image
# create scalr farm with your own project id
ansible-playbook -e "SCALR_PROJECT_ID=bfx62c5c-4d88-4b43-adff-1171b26df3d4" -e "SCALR_FARM_NAME=my-servers" scalr.yml  --tags farm
# set category and role if necessary
ansible-playbook -e "SCALR_CATEGORY_NAME=mycat" -e "SCALR_ROLE_NAME=myrole" scalr.yml --tags role
# create farm-roles with the role_id  created on last step
ansible-playbook -e "SCALR_FARM_NAME=my-servers" -e "SCALR_ROLE_ID=86208" scalr.yml --tags farm-role
# import server
ansible-playbook -e "SCALR_FARM_NAME=my-servers" scalr.yml --tags import

```

check visibility in web UI

WARNING: Farm termination kills the VMs!!!
