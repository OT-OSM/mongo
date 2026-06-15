## Steps for Backup and Restore operations with the Backup Server

Backups run on the secondary (`mongodb_backup_server=true`) using `mongodump`
with `--readPreference secondary`, so they never impact the primary.

### Step 1. Perform a Full Backup

```
sudo /usr/local/bin/mongodb-full-backup.sh
```

- `local` storage: creates a timestamped directory under `mongodb_ha_local_backup_path`
  containing a compressed `mongodump` archive, and prunes backups older than
  `mongodb_ha_backup_full_retention_days`.
- `minio` storage: streams the compressed archive directly to
  `{{ mongodb_ha_minio_bucket }}/<YYYY-MM-DD>/` and removes the local temp file.

```
Output
Backup successful!
Backup created at /var/backup/mongodb/full_20260613_010001/mongodump_20260613_010001.archive.gz
```

### Step 2. Restore from a Backup

Copy (or `mc cp` from MinIO) the archive to the target node, then run:

```
mongorestore \
  --username RootAdmin --password '<password>' --authenticationDatabase admin \
  --host 127.0.0.1:27017 \
  --gzip \
  --archive=/path/to/mongodump_<timestamp>.archive.gz \
  --drop
```

### Step 3. Re-join the Replica Set

If the node was rebuilt after a failover, re-add it to the replica set from
the current primary:

```
mongosh --authenticationDatabase admin -u RootAdmin -p '<password>' \
  --eval "rs.add('new-node-hostname:27017')"
```

The `post-failover-action.sh` script (deployed when
`mongodb_deploy_failover_script: true`) automates steps 2 and 3 — it pulls the
latest backup and runs `rs.add()` against the current primary.
