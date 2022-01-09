---
layout: post
title: "Cloud Agnostic Microservice Deployment Architecture Using Kubernetes"
date: 2021-07-27 08:46:34 +0530
comments: true
categories: ["kubernetes", "k8s", "cloud", "aws", "azure"]
---

A typical web app contains the following microservices.

> * API (Flask / FastAPI / Rails)
> * Worker (Celery / Sidekiq)
> * Frontend (React / Angular)
> * Redis (Cache)
> * Postgres (Database)

We will use kubernetes to deploy these microservices.

We won't use s3 for frontend deployment even though it is easy to deploy cloudfront + s3 combination.
But then we would have a dependency on s3 which is only available in AWS.
(You should use s3 if you know AWS is going to be your only cloud)

### Architecture ###
![Architecture]({{ site.url }}/images/k8s-arch.png)

Using this design, you just need kubernetes manifests. You can use the same in any cloud providers.
