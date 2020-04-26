# OSM: MONGODB
###### MongoDB ansible role for Standalone , Cluster(Replication) setup with Authentication

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
| mongo_root_backup_pwd | passw0rd | Override default values with your ones |mongo backup user password
|mongo_authentication_key | | Mention your key here. (GPG key) | Authentication key for cluster nodes to communicate with each other securely. |

## INVENTORY

An example inventory could be like this:-

```ini
[mongo_master]
34.245.77.60

[mongo_slave]
34.247.217.40

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