---
layout: post
title: "Creating a postgres master and slave streaming server with S3 backup"
date: 2016-09-05 13:01:13 +0530
comments: true
categories: [postgres, database, streaming, wal-e, s3]
---

### Setup

![API docs image]({{ site.url }}/images/db.png)
<sub><sup> * Image credit: google images </sub></sup>

### Master server setup

```bash

sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt/ `lsb_release -cs`-pgdg main" >> /etc/apt/sources.list.d/pgdg.list'
wget -q https://www.postgresql.org/media/keys/ACCC4CF8.asc -O - | sudo apt-key add -
sudo apt-get update
sudo apt-get install postgresql postgresql-contrib
```

Create user for replication

```bash
sudo su postgres
psql
CREATE USER replicator REPLICATION LOGIN ENCRYPTED PASSWORD 'password';
```

Edit the following line in postgresql.conf

```sql /etc/postgresql/9.5/postgresql.conf
wal_level = hot_standby
max_wal_senders = 3
wal_keep_segments = 8
```

Add the following line in pg_hba.conf

```sql /etc/postgresql/9.5/pg_hba.conf
host     replication     replicator      <slave ip>            md5
```

Restart master postgres.

### Slave server setup

Setup postgresql 9.5 in slave server.
Stop the server before chaning any configurations
```bash
sudo su postgres
service postgresql stop
```

Edit the following line in postgresql.conf

```sql /etc/postgresql/9.5/postgresql.conf
wal_level = hot_standby
hot_standby = on
```

Pull base backup from master server

```sql
rm -rf /var/lib/postgresql/9.5/main
pg_basebackup -h <master_ip> -D /var/lib/postgresql/9.5/main -U replicator -v -P
```

Create a recovery file for streaming
```sql /var/lib/postgresql/9.5/main/recovery.conf
	standby_mode = 'on'
    primary_conninfo = 'host=<master_ip> port=5432 user=replicator password=password'
    trigger_file = '/var/lib/postgresql.trigger'
```

Now start the slave server
```bash
service postgresql start
```

Run the following command in master to check whether replication is on or not.
```sql
select * from pg_stat_replication;
```

## Send Wal-E files to S3 for backup

Run the following commands to setup wal-e

```bash
sudo su
sudo apt-get install daemontools libevent-dev python-all-dev lzop pv
sudo apt-get install python-setuptools
sudo easy_install pip
sudo pip install wal-e --upgrade
```

Add your s3 credentials
```bash
mkdir -p /etc/wal-e.d/env
echo <ACCESS_KEY> > /etc/wal-e.d/env/AWS_ACCESS_KEY_ID
echo <SECRET_KEY> > /etc/wal-e.d/env/AWS_SECRET_ACCESS_KEY
echo 's3://<bucket_path>' > /etc/wal-e.d/env/WALE_S3_PREFIX
echo 'us-east-1' > /etc/wal-e.d/env/AWS_REGION
chown -R root:postgres /etc/wal-e.d
```

Create a base backup
```bash
envdir /etc/wal-e.d/env wal-e backup-push $PG_DATA
```

List all the backup files
```bash
envdir /etc/wal-e.d/env /usr/local/bin/wal-e backup-list
```

Push Latest backup from S3
```bash
envdir /etc/wal-e.d/env /usr/local/bin/wal-e backup-fetch LATEST
```

Setup daily backup push crontab 
```bash
crontab -e
/usr/bin/envdir /etc/wal-e.d/env /usr/local/bin/wal-e backup-push /e_data/main/ > /tmp/postgres_wale_backup_push.log;
#keep last 5 base backups
/usr/bin/envdir /etc/wal-e.d/env /usr/local/bin/wal-e delete --confirm retain 5 > /tmp/postgres_wale_backup_delete.log;
```

Please comment here if you face any problems during the setup.