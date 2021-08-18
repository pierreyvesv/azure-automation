---
layout: single
title:  "Force kubernetes certificates renewal"
date:   2021-04-30 22:40:46 +0100
categories: kubernetes
---

## Force kubernetes certificates renewal

```bash
kubectl get certs -n rancher --no-headers=true | awk '{print $1}' | xargs -n 1 kubectl -n rancher patch certificate --patch '
- op: replace
  path: /spec/renewBefore
  value: 1h
' --type=json
```

```bash
kubectl get certs -n rancher --no-headers=true | awk '{print $1}' | xargs -n 1 kubectl -n rancher patch certificate -n rancher --patch '
- op: remove
  path: /spec/renewBefore
' --type=json
```
source: <https://github.com/jetstack/cert-manager/issues/2641>

