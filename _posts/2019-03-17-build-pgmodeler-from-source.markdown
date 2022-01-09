---
layout: post
title: "Build PgModeler from Source in MacOS Mojave"
date: 2019-03-17 20:07:46 +0530
comments: true
categories: [db, mac, postgres, PgModeler]
---

#### Download the Source ####

Download source from [here](https://pgmodeler.io/download?source=true) <br>
Please refer [Official build document](https://pgmodeler.io/support/installation)

I had to make the following changes to make it work in my mac (MacOS Mojave)

#### Install Requirements ####

```
  brew install qt    
  brew link qt5 —force
  brew install postgresql
  brew install libxml2
```

#### Edit pgmodeler.pri ####

Open the cloned repo.  <br>

Edit `PGSQL_LIB, PGSQL_INC, XML_INC, XML_LIB` in pgmodeler.pri
<sub> Please note that the following works only if you have used brew to install the requirements </sub>

```
  PGSQL_LIB = /usr/local/opt/postgresql/lib/libpq.dylib
  PGSQL_INC = /usr/local/opt/postgresql/include
  XML_INC = /usr/local/opt/libxml2/include/libxml2
  XML_LIB = /usr/local/opt/libxml2/lib/libxml2.dylib
```

#### Build the software ####

```
export PGMODELER_ROOT=/Applications/pgmodeler.app/
qmake -r pgmodeler.pro
make
make install
macdeployqt $PGMODELER_ROOT \
                            -executable=$PGMODELER_ROOT/Contents/MacOS/pgmodeler-ch \
                            -executable=$PGMODELER_ROOT/Contents/MacOS/pgmodeler-cli
```                            
