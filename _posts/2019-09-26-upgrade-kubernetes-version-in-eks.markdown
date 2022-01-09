---
layout: post
title: "Upgrade Kubernetes Version in AWS EKS"
date: 2019-09-26 23:55:25 +0530
comments: true
categories: [aws, kubernetes, k8s]
---

There are two things you need to do to upgrade Kubernetes in EKS.

Unlike `Azure AKS` where you can upgrade with a single button, in `AWS EKS` you need
to upgrade the cluster and worker nodes separately.


### Upgrade Cluster ###

=> Login into AWS console.

=> Go to EKS services.

=> Click on Clusters

![Cluster Upgrade]({{ site.url }}/images/update_cluster.jpg)


### Upgrade Worker Nodes ###

Reference Link: https://docs.aws.amazon.com/eks/latest/userguide/update-stack.html


#### Step 1: ####

Go to Worker node cloud formation stack and click on `Update`

####  Step 2: ####

Click on "Replace current template"

Paste the following URL: https://amazon-eks.s3-us-west-2.amazonaws.com/cloudformation/2019-09-17/amazon-eks-nodegroup.yaml

Click "Next"

![Worker Node 1]({{ site.url }}/images/update_stack_1.jpg)

#### Step 3: ####

Update the following parameters
```
NodeAutoScalingGroupDesiredCapacity = NodeAutoScalingGroupDesiredCapacity
```

```
NodeAutoScalingGroupMaxSize = NodeAutoScalingGroupDesiredCapacity + 1 (or more)
(It must be more than what you have)
```


```
NodeImageIdSSMParam = /aws/service/eks/optimized-ami/`1.14`/amazon-linux-2/recommended/image_id
(If your cluster version is 1.13 change it to 1.13. Ideally it should match with the Cluster version. Otherwise the upgrade won't work.
You will end up in `matchnodeselector` error and `NotReady` status)

```

Leave the other fields to default

Click "Next"

![Worker Node 2]({{ site.url }}/images/update_stack_2.jpg)

#### Step 4: ####

Go to last page and check the box. Click "Update"

![Worker Node 3]({{ site.url }}/images/update_stack_3.jpg)


Once the stack is updated, Connect to your cluster. You should see your version upgraded to 1.14

`kubectl get nodes`

![Final]({{ site.url }}/images/final.jpg)
