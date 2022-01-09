---
layout: post
title: "Elasticsearch Backup and Restore from Azure Blob Storage"
date: 2017-11-09 13:00:26 -0500
comments: true
categories: devops, elasticsearch
---

Assumptions:

You already have a working ELK cluster (5.x). 

Azure Account


### Step 1: Storage account ###

Create Storage Account:

![ES1]({{ site.url }}/images/es1.jpg)
<!-- ![ES1](http://localhost:4000/images/es1.jpg)  -->

### Step 2: Get Credentials###

Get Storage account name and key

![ES2]({{ site.url }}/images/es2.jpg)
<!-- ![ES2](http://localhost:4000/images/es2.jpg)  -->

### Step 3: Install azure plugin###

Ssh into all elastic search nodes. 

Go to `/usr/share/elasticsearch/`

Run `sudo bin/elasticsearch-plugin install repository-azure`


### Step 4: Update config###

Go to `/etc/elasticsearch/elasticsearch.yml`. Add your Azure configuration
![ES3]({{ site.url }}/images/es3.jpg)
<!-- ![ES3](http://localhost:4000/images/es3.jpg)  -->

Restart `sudo service elasticsearch restart`


### Step 5: Create snapshots###

Open Kibana portal and Click on `Dev Tools`

Configure Repository
```bash
PUT _snapshot/es_snapshot
{
    "type": "azure"
}

```

Create Backup
```bash
PUT _snapshot/es_snapshot/mybackup_1
```

List snapshots
```bash
GET /_snapshot/es_snapshot/mybackup_1
```


### Step 6: ###

Go to Storage account. Click on "Containers" to see the snapshots.

![ES4]({{ site.url }}/images/es4.jpg)
<!-- ![ES4](http://localhost:4000/images/es4.jpg)  -->


## Restore from Azure storage account ##

### Step 7: ###

Follow step 1 to step 4 to configure your new cluster. 


### Step 8: ###

Close all the indices
```
POST /_all/_close
```

### Step 9: ###

Restore from snapshot

```
POST /_snapshot/es_snapshot/mybackup_1/_restore
```
