---
layout: post
title: "Send Hipchat notifications using python"
date: 2017-03-07 18:57:53 +0530
comments: true
categories: python, devops, hipchat
---

There might be scenarios where you want to send hipchat notifications programatically. 
Following python script will help you to create notifications in 3 simple steps.

![Hipchat Image]({{ site.url }}/images/hipchat.png)
<sub><sup> * Image credit: google images </sub></sup>
<!-- ![Hipchat Image](http://localhost:4000/images/hipchat.png) -->

### Step 1:

Generate your auth token from your hipchat account.
`Hipchat -> Account Settings -> API Access`

### Step 2:

Note: I am using hipchat api v1.

```python hipchat.py
#!/usr/bin/env python3.5

import requests, json

def notify(message, deployer = 'Vishnu', color = 'green'):
	url = "https://api.hipchat.com/v1/rooms/message?auth_token=<auth_token>&format=json"
	payload = { "room_id" : "deployments" , "from" : deployer, "message" : message, "color" : color, "notify" : 1 }
	requests.post(url, params=payload)
```

### Step 3:

```python deployer.py
#!/usr/bin/env python3.5

import hipchat

# For successful notif
hipchat.notify("Deployment started", "Vishnu", "green")

# If you want error notif
hipchat.notify("Deployment failed", "Vishnu", "red")

```

