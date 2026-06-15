# What's New

A production-grade Ansible role to install, configure, and manage **MongoDB 8.0** on Ubuntu/RHEL with Primary-Secondary replication, optional Arbiter, authentication, mongodump-based backups (MinIO or local), mongodb_exporter metrics, and PMM integration.

### v2.0.0
##### June 2026

#### :tada: New Features

##### [ADD]
- Restructured role to the OT-OSM HA pattern (defaults/handlers/meta/tasks/templates/tests/vars)
- All variables prefixed with `mongodb_` for feature toggles
- Replica set (Primary/Secondary) automation with bootstrap auth flow
- Optional Arbiter support (PSA topology)
- mongodump backup scripts — MinIO or local disk, with cron
- mongodb_exporter for Prometheus metrics (togglable)
- PMM (Percona Monitoring and Management) integration (togglable)
- Post-failover resync helper script

### v1.0.0
##### June 10, 2026

#### :tada: New Features

##### [ADD]
- MongoDB 8.0 support
- Ubuntu 24.04 support
- MongoDB Shell (mongosh) support

##### [UPDATE]
- Updated MongoDB repositories for MongoDB 8.0
- Updated documentation

### v0.0.2
##### March 21, 2022

#### :tada: New Features

##### [ADD]
- Manage node hostname from role
