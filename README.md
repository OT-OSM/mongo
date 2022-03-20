# OSM: MONGODB
###### MongoDB ansible role for Standalone , Cluster(Replication) setup with Authentication

Version History
------------------
|**Date**| **Version**| **Description**| **Changed By** |
|----------|---------|---------------|-----------------|
|**27 June 2020** | v0.0.1 | Initial draft | Rajat Vats |

### PREREQUISITE
* ###### NOTHING !!!!

### OS-SUPPORTED
* ###### Ubuntu 18
* ###### Centos 7
* ###### Amazon Linux 2

### FEATURES
* Setup Standalone MongoDB instance
* Setup Replication over mongodb cluster
* Setup Authentication with root,admin and backup users.

## IMPORTANT ROLE VARIABLES

|**Variable**|**Default Value**|**Possible Values**|**Description**|
|------------|-----------------|-------------------|---------------|
| mongo_version | 4.2 | [4.2, 4.0, 3.6, 3.4] Can mention minor version as well eg: 4.2.7 |MongoDB version | 
| mongo_port | 27017 | As per your requirement | MongoDB port |
| replication_enabled | false | [true, false]  |Set True to setup replication
| mongo_replSetName | repl | As per your requirement | Name of the replica set|
| authentication_enabled | true | [true, false] | If set true, role will enable authetication with predefined uses -> root , admin and backup user|
| mongo_user_admin_name | useradmin | Override default values with your ones | mongo admin user name|
| mongo_user_admin_pwd | passw0rd |  Override default values with your ones|mongo admin user password
| mongo_root_admin_name | RootAdmin |  Override default values with your ones|mongo root user name|
| mongo_root_admin_pwd | passw0rd |  Override default values with your ones|mongo root user password
| mongo_root_backup_name | backupuser | Override default values with your ones |mongo backup user name|
| mongo_root_backup_pwd | passw0rd | Override default values with your ones |mongo backup user password |
| mongo_custom_users | None | Can specify multiple users with roles. Please refer below example for the reference |
|mongo_authentication_key | | Mention your key here. (GPG key) | Authentication key for cluster nodes to communicate with each other securely. |

### Define Multiple users to add in MongoDB
    mongo_custom_users:
        - { name: user1 , password: userpwd, roles: admin }
        - { name: user2 , password: userpwd, roles: backup,clusterMonitor }


## INVENTORY

An example inventory could be like this:-

```ini

[mongo_master]
prim ansible_ssh_host=3.1.x.x ansible_user=ubuntu

[mongo_slave]
sec1 ansible_ssh_host=18.141.x.x ansible_user=ubuntu
sec2 ansible_ssh_host=54.169.x.x ansible_user=ubuntu

[mongo:children]
mongo_master
mongo_slave

```

## PLAYBOOK EXAMPLE

Here is an example of playbook to execute this role:-

```yaml
---
- hosts: mongo
  roles:
    - osm_mongo
  become: true
  any_errors_fatal: true

```

## USAGE
```shell
 ansible-playbook -i hosts site.yml
 ```
## AUTHOR

**[Rajat Vats](rajat.vats@opstree.com)**
