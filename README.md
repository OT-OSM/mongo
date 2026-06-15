[![Apache License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](LICENSE)
![GitHub release (latest by date)](https://img.shields.io/github/v/release/OT-OSM/mongodb_server)

[![Opstree Solutions][opstree_avatar]][opstree_homepage]<br/>[Opstree Solutions][opstree_homepage]

  [opstree_homepage]: https://opstree.github.io/
  [opstree_avatar]: https://img.cloudposse.com/150x150/https://github.com/opstree.png

# MongoDB Server HA

A production-grade Ansible role to install, configure, and manage **MongoDB 8.0** on Ubuntu / RHEL with Primary-Secondary replication (optional Arbiter for PSA topology), authentication and user lifecycle management, mongodump-based backup to MinIO or local disk, Prometheus metrics via mongodb_exporter, and PMM integration.

## Key Features

- [x] MongoDB 8.0 installation on Ubuntu 20.04 / 22.04 / 24.04, RHEL/CentOS 7/8, Amazon Linux 2
- [x] Primary-Secondary replica set with auto-bootstrap of admin users
- [x] Optional Arbiter node — PSA (Primary-Secondary-Arbiter) topology
- [x] Authentication — root, admin, backup, and custom application users
- [x] mongodump — full backup, weekly retention
- [x] Backup storage toggle — MinIO (object storage) or local disk
- [x] MinIO client (mc) installation and configuration
- [x] mongodb_exporter for Prometheus metrics (togglable)
- [x] PMM (Percona Monitoring and Management) integration (togglable)
- [x] Post-failover resync script for secondary/arbiter recovery
- [x] Idempotent — safe to re-run without side effects
- [x] All variables prefixed with `mongodb_` — no conflicts with other roles

---

## Requirements

- Ubuntu `focal`/`jammy`/`noble` or RHEL/CentOS 7/8 or Amazon Linux 2
- Root/sudo access on target hosts
- `community.mongodb` Ansible collection:

```bash
ansible-galaxy collection install community.mongodb
```

> **Security Note:** All passwords have placeholder defaults in `defaults/main.yml`.
> **Always override passwords via Semaphore environment variables or Ansible Vault.**

---

## Role Variables

### 🔧 Feature Toggles — MongoDB Core

| Variable | Default | Description |
|---|---|---|
| `mongodb_install_packages` | `true` | Install MongoDB packages |
| `mongodb_replication` | `false` | Enable Primary/Secondary replica set |
| `mongodb_authentication_enabled` | `true` | Enable authentication and create HA users |
| `mongodb_users_creation` | `true` | Run HA user creation tasks |
| `mongodb_root_password_update` | `false` | Force update of root admin password |

### 🔧 Feature Toggles — HA Components

| Variable | Default | Description |
|---|---|---|
| `mongodb_arbiter_configure` | `false` | Add an arbiter node to the replica set |
| `mongodb_metrics_enabled` | `false` | Install and configure mongodb_exporter |
| `mongodb_pmm_enabled` | `false` | Enable PMM monitoring |
| `mongodb_minio_configure` | `false` | Install and configure mc client |
| `mongodb_deploy_backup_scripts` | `false` | Deploy backup scripts and cron |
| `mongodb_setup_backup_cron` | `false` | Enable backup cron jobs |
| `mongodb_backup_server` | `false` | Mark node as backup server |
| `mongodb_deploy_failover_script` | `false` | Deploy post-failover resync script |

### 🔐 Credentials — Override via Vault

| Variable | Default | Description |
|---|---|---|
| `mongo_root_admin_name` / `mongo_root_admin_pwd` | `RootAdmin` / `changeme` | Cluster root user |
| `mongo_user_admin_name` / `mongo_user_admin_pwd` | `useradmin` / `changeme` | User admin (userAdminAnyDatabase) |
| `mongo_root_backup_name` / `mongo_root_backup_pwd` | `backupuser` / `changeme` | Backup + clusterMonitor user |
| `mongo_custom_users` | `None` | List of `{name, password, roles, database}` |
| `mongo_authentication_key` | `changeme...` | Replica set internal auth keyfile content |
| `mongodb_ha_minio_root_password` | `changeme` | MinIO root password |
| `mongodb_exporter_db_password` | `{{ mongo_root_backup_pwd }}` | mongodb_exporter DB password |
| `mongodb_ha_pmm_password` | `changeme` | PMM admin password |

### 🛠 MongoDB Connection & General

| Variable | Default | Description |
|---|---|---|
| `mongo_port` | `27017` | MongoDB listen port |
| `mongo_bindIp` | `0.0.0.0` | MongoDB bind address |
| `mongo_db_path` | `/var/lib/mongodb` (Debian) / `/var/lib/mongo` (RHEL) | Data directory |
| `mongo_service_name` | `mongod` | MongoDB service name |
| `mongodb_master_group` | `mongo_master` | Inventory group for the primary |
| `mongodb_slave_group` | `mongo_slave` | Inventory group for the secondary |
| `mongodb_arbiter_group` | `mongo_arbiter` | Inventory group for the arbiter |

### 📦 Replication

| Variable | Default | Description |
|---|---|---|
| `mongo_replSetName` | `rs0` | Replica set name |
| `master_priority` | `5` | Primary member priority |
| `slave_priority` | `1` | Secondary member priority |
| `arbiter_priority` | `0` | Arbiter member priority |
| `mongo_heartbeat_timeout_secs` | `10` | Replica set heartbeat timeout |
| `mongo_election_timeout_millisec` | `10000` | Replica set election timeout |

### 📈 Prometheus Exporter

| Variable | Default | Description |
|---|---|---|
| `mongodb_exporter_version` | `0.40.0` | mongodb_exporter version |
| `mongodb_exporter_user` | `mongodb_exporter` | OS system user |
| `mongodb_exporter_port` | `9216` | Metrics port |
| `mongodb_exporter_db_user` | `{{ mongo_root_backup_name }}` | Mongo user used by the exporter |

### 💾 Backup Settings

| Variable | Default | Description |
|---|---|---|
| `mongodb_ha_backup_storage` | `minio` | Storage type — `minio` or `local` |
| `mongodb_ha_backup_log_file` | `/var/log/mongodb-backup.log` | Backup log file |
| `mongodb_ha_backup_full_retention_days` | `7` | Full backup retention days |
| `mongodb_ha_local_backup_path` | `/var/backup/mongodb` | Local backup path (local only) |
| `mongodb_ha_backup_full_cron_hour` / `_minute` / `_weekday` | `1` / `0` / `*` | Backup cron schedule |

---

## Inventory

```ini
# =============================================================================
# MongoDB HA — Inventory
# =============================================================================
# Notes:
#   - Leave [mongo_arbiter] empty for a standard 2-node primary/secondary setup
#   - Set mongodb_arbiter_configure=true and populate [mongo_arbiter] for a
#     3-node PSA (Primary-Secondary-Arbiter) setup
#   - Set mongodb_backup_server=true on the secondary that runs mongodump
# =============================================================================

[mongo_master]
mongo_primary

[mongo_slave]
mongo_secondary mongodb_backup_server=true

[mongo_arbiter]
# mongo_arbiter

[mongo:children]
mongo_master
mongo_slave
mongo_arbiter

[mongo:vars]
ansible_user=ubuntu
```

> **Notes:**
> - Leave `[mongo_master]`/`[mongo_slave]` with a single host each for a 2-node Primary/Secondary setup.
> - Populate `[mongo_arbiter]` and set `mongodb_arbiter_configure: true` for a PSA topology.
> - Set `mongodb_backup_server=true` on the secondary that should run `mongodump`.

---

## Example Playbook

```yaml
---
# =============================================================================
# MongoDB HA — Primary/Secondary + optional Arbiter, Backup, Metrics, PMM
# =============================================================================
- name: Configure MongoDB Cluster
  hosts: mongo
  become: true
  roles:
    - mongodb_server
```

### With Vault-encrypted passwords

```yaml
# playbooks/dev/mongodb-server/vars/vars.yml
mongodb_replication: true
mongodb_authentication_enabled: true

# Secrets — loaded from Semaphore environment variables
mongo_root_admin_pwd:  "{{ lookup('env', 'VAULT_MONGO_ROOT_PASSWORD') }}"
mongo_user_admin_pwd:  "{{ lookup('env', 'VAULT_MONGO_USERADMIN_PASSWORD') }}"
mongo_root_backup_pwd: "{{ lookup('env', 'VAULT_MONGO_BACKUP_PASSWORD') }}"
mongo_authentication_key: "{{ lookup('env', 'VAULT_MONGO_KEYFILE') }}"

mongodb_ha_minio_root_password: "{{ lookup('env', 'VAULT_MINIO_PASSWORD') }}"
mongodb_ha_pmm_password:        "{{ lookup('env', 'VAULT_PMM_PASSWORD') }}"
```

---

## Backup Storage Toggle

```yaml
# MinIO — streams backup directly to object storage (default)
mongodb_ha_backup_storage: "minio"

# Local disk — stores backup on local filesystem
mongodb_ha_backup_storage: "local"
mongodb_ha_local_backup_path: "/var/backup/mongodb"
```

### MinIO Backup Structure

```
mongodb-backup/
├── 2026-06-11/
│   └── mongodump_20260611_010001.archive.gz
├── 2026-06-12/
│   └── mongodump_20260612_010001.archive.gz
├── 2026-06-13/
│   └── mongodump_20260613_010001.archive.gz
└── failover-resync/
```

---

## Usage

```bash
# Full HA setup (Primary + Secondary, replication, auth)
mongodb-server/playbook.yml

# Configuration only — skip package installation
mongodb-server/playbook.yml \
  -e "mongodb_install_packages=false"

# Create/update HA users only
mongodb-server/playbook.yml \
  --tags "create_user"

# Create custom application users/databases only
mongodb-server/playbook.yml \
  --tags "create_database"

# Deploy backup scripts only
mongodb-server/playbook.yml \
  --tags "backup_scripts"

# Deploy metrics exporter only
mongodb-server/playbook.yml \
  --tags "mongodb_metrics"

# Enable PMM monitoring
mongodb-server/playbook.yml \
  -e "mongodb_pmm_enabled=true" --tags "pmm"
```

---

## References

- [MongoDB Server Documentation](https://www.mongodb.com/docs/manual/)
- [MongoDB Replica Set Documentation](https://www.mongodb.com/docs/manual/replication/)
- [mongodump / mongorestore Documentation](https://www.mongodb.com/docs/database-tools/mongodump/)
- [community.mongodb Collection](https://galaxy.ansible.com/ui/repo/published/community/mongodb/)
- [MinIO Client Documentation](https://min.io/docs/minio/linux/reference/minio-mc.html)
- [PMM Documentation](https://docs.percona.com/percona-monitoring-and-management/)
- [mongodb_exporter](https://github.com/percona/mongodb_exporter)

---

## Author

**[Kanimozhi Viswanathan](kanimozhi.viswanathan@opstree.com)**

**[Rajat Vats](rajat.vats@opstree.com)**

**[Kritarth Pant](kritarth.pant@opstree.com)**
