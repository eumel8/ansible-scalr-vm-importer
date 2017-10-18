Scalr VM Importer
=================

Abstract
--------

Sometimes you want to riding the horse backwards. 
[Scalr](https://www.scalr.com/) is an Enterprise Grade Cloud Management 
Platform. Key feature is the cost control in multi cloud environments.
For this reason Scalr manages all resources like virtual machines (VM)
and storage. With the Scalr VM Importer current running VMs can import
in Scalr to control the costs.

Environments
------------

OpenStack/Open Telekom Cloud (easy to adapt)

Requirements
------------

Ansible 2.4+
python-pip


Workflows
---------

### Install and Configure OpenStack and Scalr Clients

``` 
    ansible-playbook -e@credentials.yml scalr.yml --tags installation,configuration
``` 

### Register Images

```
    ansible-playbook  scalr.yml --tags image
```

### Create Scalr Farm

```
    ansible-playbook -e "SCALR_PROJECT_ID=bfx62c5c-4d88-4b43-adff-1171b26df3d4" -e "SCALR_FARM_NAME=my-servers" scalr.yml  --tags farm
```

### Create Scalr Category and Role (with scope Environment)

```
    ansible-playbook -e "SCALR_CATEGORY_NAME=mycat" -e "SCALR_ROLE_NAME=myrole" scalr.yml --tags role
```

### Create Scalr Farm-Roles

```
    ansible-playbook -e "SCALR_FARM_NAME=my-servers" -e "SCALR_ROLE_ID=86208" scalr.yml --tags farm-role
```

### Import VMs to Scalr Farm

```
    ansible-playbook -e "SCALR_FARM_NAME=my-servers" scalr.yml --tags import
```

Import is required to run after each VM creation. Destroyed VMs will
automatically removed in Scalr after a while.

#### Background

The scalr role is using scalr-ctl client and openstack client to get 
and push the information to the API service. 
In OpenStack typical call looks like

```
#openstack  --os-cloud default server list | grep my-servers

| f5eba558-07d5-4e89-970c-b71d55f14c18 | log00-my-servers      | ACTIVE | 35661a60-bf98-49c4-b6bb-b6d72e773ce3=10.11.66.64  |
| ce5b780d-4898-4fae-a47c-b3944f4af976 | core01-my-servers     | ACTIVE | 35661a60-bf98-49c4-b6bb-b6d72e773ce3=10.11.66.33  |
| 9e574ceb-1d80-41f2-93a8-ce566d5f0ccc | deploy01-my-servers   | ACTIVE | 35661a60-bf98-49c4-b6bb-b6d72e773ce3=10.11.66.42  |
| 40b956bd-0ff1-4136-a8b8-81f4554479ca | proxy00-my-servers    | ACTIVE | 35661a60-bf98-49c4-b6bb-b6d72e773ce3=10.11.66.10  |
| dcc49256-2f87-4361-ab3c-972b5aef3dc3 | deploy00-my-servers   | ACTIVE | 35661a60-bf98-49c4-b6bb-b6d72e773ce3=10.11.66.40  |
| 8db801a9-131c-4dc0-af4a-c68536b2a486 | frontend01-my-servers | ACTIVE | 35661a60-bf98-49c4-b6bb-b6d72e773ce3=10.11.66.129 |
| d5b61de8-fcf4-4fd3-b16a-7bfa32c7f9d0 | frontend00-my-servers | ACTIVE | 35661a60-bf98-49c4-b6bb-b6d72e773ce3=10.11.66.128 |
| 792a1c2a-d9e6-4c18-b41b-e3bd71391d0c | monitor00-my-servers  | ACTIVE | 35661a60-bf98-49c4-b6bb-b6d72e773ce3=10.11.66.96  |
| 5fd6d718-29f5-41a9-b916-684a5793a11e | vpn01-my-servers      | ACTIVE | 35661a60-bf98-49c4-b6bb-b6d72e773ce3=10.11.66.9   |
| 6f17efc7-b6e4-4b16-9352-a549202b0147 | proxy01-my-servers    | ACTIVE | 35661a60-bf98-49c4-b6bb-b6d72e773ce3=10.11.66.11  |
| be20561c-e07d-412b-9596-26034ae20be9 | vpn00-my-servers      | ACTIVE | 35661a60-bf98-49c4-b6bb-b6d72e773ce3=10.11.66.8   |

```

This collects servers with the same name part into a scalr farm 
with the same name ('my-servers'). 
Name part could be also a sort of servers, i.e. 'webserver'

##### This is a PoC. Don't try this at home! Prevent access by your kids!
