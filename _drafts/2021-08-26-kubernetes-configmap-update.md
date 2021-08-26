---
layout: single
title:  "Deployment restart when configmap is update"
date:   2021-08-26 22:40:46 +0100
categories: kubernetes
---

# Kubernetes update configmap

## Introduction

Changing config map don't trigger a redeploy.

The trick is to build a hash from the configmap and update the deployment with it.

Once the hash is update a dependency in the `Deployment` container will redeploy the pods

this tuto is from <https://blog.questionable.services/article/kubernetes-deployments-configmap-change/> but it was not working as it is. The field path below has been updated

## install yq

<https://github.com/mikefarah/yq>,  I have used the apt linux version.

## deployment and configmap

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: octave-mobi8-deployment
  namespace: octave-mobi8
  labels:
    app: octave-mobi8-deployment
spec:
  replicas: 2
  revisionHistoryLimit: 3
  selector:
    matchLabels:
      app: nginxapp
  template:
    metadata:
      labels:
        app: nginxapp
      annotations:
        # This field is update with the yq command
        configHash: a4e40e437a7e30a5b8525a840cd874424ffc097e89996a050b2046db594ff043
    spec:
      containers:
          name: nginx
          image: nginx:alpine
          ports:
            - containerPort: 443
          envFrom:
            - # The ConfigMap we want to use
              configMapRef:
                name: nginx-conf
          env:
            - name: CONFIG_HASH
              valueFrom:
                fieldRef:
                  fieldPath: metadata.annotations['configHash']
```

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: nginx-conf
data:
  nginx.conf: |-
    user  nginx;
    worker_processes  1;

    error_log  /var/log/nginx/error.log warn;
    pid        /var/run/nginx.pid;

    events {
        worker_connections  1024;
    }

    http {
        your config here
        }
```
