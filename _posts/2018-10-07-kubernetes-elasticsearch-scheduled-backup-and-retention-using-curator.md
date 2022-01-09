---
layout: post
title: "Kubernetes- Elasticsearch scheduled backup and retention using Curator"
date: 2018-10-07 21:54:58 +0530
comments: true
categories: ["Elasticsearch", "Kubernetes", "Curator", "Cronjob"]
---

This post will guide you to create a scheduled elasticsearch backup using curator in k8s cluster.

#### Assumptions:
```
You already have a 6.x.x elasticsearch k8s cluster
You already have a backup system
You are some what familiar with Kubernetes
You are familiar with curator configs
```
#### Step 1: Create a configmap with curator yml

```
Action 1 will create a snapshot
Action 2 will keep last 20 days snapshots and delete that are older than 20 days
```

curator.yml
```
apiVersion: v1
kind: ConfigMap
metadata:
  name: curator-config
  labels:
    app: curator
data:
  backup.yml: |-
    ---
    # Remember, leave a key empty if there is no value.  None will be a string,
    # not a Python "NoneType"
    #
    # Also remember that all examples have 'disable_action' set to True.  If you
    # want to use this action as a template, be sure to set this to False after
    # copying it.
    actions:
      1:
        action: snapshot
        description: "Create snapshot"
        options:
          repository: "primary_backup"
          continue_if_exception: False
          wait_for_completion: True
        filters:
          - filtertype: pattern
            kind: regex
            value: ".*$"
      2:
        action: delete_snapshots
        description: >-
          Delete snapshots from the selected repository older than 10 days
          (based on creation_date), for 'curator-' prefixed snapshots.
        options:
          repository: "primary_backup"
          disable_action: False
        filters:
          - filtertype: pattern
            kind: prefix
            value: curator-
            exclude:
          - filtertype: age
            source: creation_date
            direction: older
            unit: days
            unit_count: 20
  config.yml: |-
    ---
    # Remember, leave a key empty if there is no value.  None will be a string,
    # not a Python "NoneType"
    client:
      hosts:
        - elasticsearch
      port: 9200
      url_prefix:
      use_ssl: False
      certificate:
      client_cert:
      client_key:
      ssl_no_validate: False
      http_auth:
      timeout: 30
      master_only: False
    logging:
      loglevel: DEBUG
```

#### Step 2: Create a config map in your kubernetes cluster
```
kubectl apply -f curator.yml
```

#### Step 3: Create a cronjob

es_cronjob.yml

```
apiVersion: batch/v1beta1
kind: CronJob
metadata:
  name: esbackup
  labels:
    app: esbackup
spec:
  schedule: "@daily"
  successfulJobsHistoryLimit: 1
  failedJobsHistoryLimit: 3
  concurrencyPolicy: Forbid
  startingDeadlineSeconds: 120
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - image: bobrik/curator:5.5.4
            name: curator
            args: ["--config", "/etc/config/config.yml", "/etc/config/backup.yml"]
            volumeMounts:
            - name: config
              mountPath: /etc/config
          volumes:
          - name: config
            configMap:
              name: curator-config
          restartPolicy: Never
```

#### Step 4: Create cronjob in kubernetes
This cronjob will run everyday 00:00 hrs
```
kubectl create -f es_cronjob.yml
```

Reference:

* https://medium.com/@hagaibarel/running-curator-as-a-kubernetes-cronjob-19eaab9afd3b
* https://www.elastic.co/guide/en/elasticsearch/client/curator/current/delete_snapshots.html
