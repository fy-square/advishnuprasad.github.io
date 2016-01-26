---
layout: post
title: "Multiple solr servers in single machine"
date: 2014-04-26 10:52:00 +0530
comments: true
categories: [solr]
---

#### Step 1:
Go to your solr directory.

```bash
cd /opt/solr141/
sudo mkdir home1
copy contents into home1 directory from other home directory.
sudo cp -a /opt/solr141/home/. /opt/solr141/home1
```

#### Step 2:

create `solr1.xml` under `tomcat6/conf/catalina/localhost.` Copy from `solr.xml` and just change value to point to new home dir in environment tag
 
```xml
<!--?xml version="1.0" encoding="utf-8" ?-->
<Context docBase="/opt/solr/solr141/dist/apache-solr-1.4.1.war" debug="0" crossContext="true" > 
<Environment name="solr/home" type="java.lang.String" value="/opt/solr/solr141/home1" override="true" /> 
</Context>
```

#### Step 3:

```bash /opt/solr/solr141/home1/conf/Solr-config.xml 
<dataDir>${solr.data.dir:/opt/solr/solr141/home1/data}</dataDir> 
<locktype>simple</locktype> 
<unlockonstartup>true</unlockonstartup>
```

#### Step 4:

Restart tomcat
```bash
sudo service tomcat6 restart
```

#### Step 5:

Since we copied from other home folder, we need to clean up the index

```bash
curl localhost:7585/solr1/update --data '*:*' -H 'Content-type:text/xml; charset=utf-8'
```

There you go.

`localhost:8080/solr1` should be up and running