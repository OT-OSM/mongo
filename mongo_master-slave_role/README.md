# OSM: MONGODB
MongoDB ansible role for Standalone , Cluster(Replication) setup with Authentication
=====================================

[![Opstree Solutions][opstree_avatar]][opstree_homepage]

[Opstree Solutions][opstree_homepage] 

  [opstree_homepage]: https://opstree.github.io/
  [opstree_avatar]: https://img.cloudposse.com/150x150/https://github.com/opstree.png

Version History
------------------
|**Date**| **Version**| **Description**| **Changed By** |
|----------|---------|---------------|-----------------|
|**23 may 2022** | v0.0.1 | Initial draft | Reena nain |

### PREREQUISITE
* ###### NOTHING !!!!

### OS-SUPPORTED
* ###### Ubuntu 18 and 20


### FEATURES
* Setup Standalone MongoDB instance
* Setup Replication over mongodb cluster
* Setup Authentication with root and admin users.

## IMPORTANT ROLE VARIABLES

|**Variable**|**Default Value**|**Possible Values**|**Description**|
|------------|-----------------|-------------------|---------------|
| mongo_version | 4.2 | [5.0,4.2, 4.0, 3.6] Can mention minor version as well eg: 4.2.7 |MongoDB version | 
| mongo_port | 27017 | As per your requirement | MongoDB port |
| replication_enabled | true | [true, false]  |Set True to setup replication
| mongo_replSetName | osm_mongo | As per your requirement | Name of the replica set|
| authentication_enabled | true | [true, false] | If set true, role will enable authetication with predefined uses -> root and admin users|
| username | UserAdmin | Override default values with your ones | mongo admin user name|
| admin_pwd | Password@1234 |  Override default values with your ones|mongo admin user password
| root_adminuser | RootAdmin |  Override default values with your ones|mongo root user name|
| root_adminpass | azqGepX8xn |  Override default values with your ones|mongo root user password
|mongo_authentication_key | | Mention your key here. (GPG key) | Authentication key for cluster nodes to communicate with each other internally. |

### Define Multiple users to add in MongoDB
    mongo_custom_users:
        - { name: user1 , password: userpwd, roles: admin }
        - { name: user2 , password: userpwd, roles: backup,clusterMonitor }


## INVENTORY

An example inventory could be like this:-

```ini

[master]
prim ansible_ssh_host=3.1.x.x

[secondary]
sec1 ansible_ssh_host=18.141.x.x
sec2 ansible_ssh_host=54.169.x.x 

[mongo:children]
master
secondary


[mongo:vars]
ansible_user=ubuntu
ansible_ssh_private_key_file=/Downloads/osm.pem
ansible_python_interpreter=/usr/bin/python3
```

## PLAYBOOK EXAMPLE

Here is an example of playbook to execute this role:-

```yaml
---
- name: Install MongoDB in target machine
  hosts: mongo
  gather_facts: yes
  become: yes
  roles:
    - mongo-slave_role
```
## USAGE
```shell
 ansible-playbook -i inventory site.yml
 ```
## AUTHOR

**[Reena nain](reena.rani@opstree.com)**

|  [![Reena Nain][Reena_avatar]][Reena_homepage]<br/>[Reena Nain][Reena_homepage] |
|---|


  [reena_homepage]: https://www.linkedin.com/in/reena-nain-616b97213 
  [reena_avatar]: https://gitlab.com/uploads/-/system/user/avatar/9292330/avatar.png?width=400
