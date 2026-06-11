# OSM: MONGODB

###### MongoDB ansible role for Standalone , Cluster(Replication) setup with Authentication

Version History
------------------

|**Date**| **Version**| **Description**| **Changed By** |
|----------|---------|---------------|-----------------|
|**27 June 2020** | v0.0.1 | Initial draft | Rajat Vats |
|**21 March 2022** | v0.0.2 | Manage node DNS | Kritarth Pant |
|**10 June 2026** | v1.0.0 | MongoDB 8.0 Upgrade & Ubuntu 24.04 Support | Kanimozhi Viswanathan |

### PREREQUISITE

* ###### NOTHING !!!!

### OS-SUPPORTED

* ###### Ubuntu 24.04
* ###### Ubuntu 22.04
* ###### Ubuntu 20.04
* ###### Centos 7
* ###### Amazon Linux 2

### FEATURES

* Setup Standalone MongoDB instance
* Setup Replication over mongodb cluster
* Setup Authentication with root,admin and backup users.

## IMPORTANT ROLE VARIABLES

|**Variable**|**Default Value**|**Possible Values**|**Description**|
|------------|-----------------|-------------------|---------------|
| mongo_version | 8.0 | [8.0] Can mention minor version as well eg: 8.0.10 | MongoDB version |
| mongo_port | 27017 | As per your requirement | MongoDB port |
| replication_enabled | false | [true, false] | Set True to setup replication |
| mongo_replSetName | repl | As per your requirement | Name of the replica set |
| authentication_enabled | true | [true, false] | If set true, role will enable authetication with predefined uses -> root , admin and backup user |
| mongo_user_admin_name | useradmin | Override default values with your ones | mongo admin user name |
| mongo_user_admin_pwd | passw0rd | Override default values with your ones | mongo admin user password |
| mongo_root_admin_name | RootAdmin | Override default values with your ones | mongo root user name |
| mongo_root_admin_pwd | passw0rd | Override default values with your ones | mongo root user password |
| mongo_root_backup_name | backupuser | Override default values with your ones | mongo backup user name |
| mongo_root_backup_pwd | passw0rd | Override default values with your ones | mongo backup user password |
| mongo_custom_users | None | Can specify multiple users with roles. Please refer below example for the reference |
| mongo_authentication_key | | Mention your key here. (GPG key) | Authentication key for cluster nodes to communicate with each other securely. |

### Define Multiple users to add in MongoDB

```yaml
mongo_custom_users:
  - { name: appuser, password: "<PASSWORD>", roles: readWrite }
  - { name: monitoring, password: "<PASSWORD>", roles: clusterMonitor }
```
