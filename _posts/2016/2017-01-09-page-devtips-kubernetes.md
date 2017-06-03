---
layout: post
title:  "Kubernetes cheasheet"
date:   2017-01-09 22:18:00
categories: cheatsheet,kubernetes,devops
comments: true
---
**Check Cluster Status**

```bash
kubectl cluster-info

```

**Run an RC**

`kubectl create -f redis-master-controller.yaml`

**Check if running**

```bash
kubectl get rc
kubectl get pods
docker ps | grep redis
kubectl get services
kubectl describe service redis-master
```