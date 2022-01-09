---
layout: post
title: "Upgrade Jenkins using Shell Script"
date: 2019-10-01 18:02:17 +0530
comments: true
categories: ["jenkins", "shell", "automation"]
---

#### Script ####

```bash upgrade_jenkins.sh

VERSION=$1
echo "Upgrading jenkins to $VERSION"


echo "Downloading the jar file"
wget http://updates.jenkins-ci.org/download/war/$VERSION/jenkins.war

echo "Stopping jenkins"
service jenkins stop

sleep 5

echo "Moving the existing jenkins jar file"
TIMESTAMP=`date '+%s'`
mv /usr/share/jenkins/jenkins.war /usr/share/jenkins/jenkins.$TIMESTAMP.war

echo "Copying the latest file"
mv jenkins.war /usr/share/jenkins/jenkins.war

echo "Starting Jenkins"
service jenkins start

echo "Upgrade Successful"

```

#### Execute Shell ####
```bash
chmod +x upgrade_jenkins.sh
./upgrade_jenkins.sh <Version Number>
Exammple: ./upgrade_jenkins.sh 2.198
```

#### Sample output ####
```bash
Upgrading jenkins to 2.198
Downloading the jar file
Saving to: 'jenkins.war'   
2019-10-01 12:35:52 (5.06 MB/s) - 'jenkins.war' saved [80113050/80113050]
Stopping jenkins
Moving the existing jenkins jar file
Copying the latest file
Starting Jenkins
Upgrade Successful
```
