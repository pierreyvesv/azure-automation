---
layout: single
title:  "Rancher namespace has to be cattle-system"
date:   2021-04-30 22:40:46 +0100
categories: aks
---

## Rancher namespace must be cattle-system

"tls-rancher" secret is stored in cattle system , installing rancher in an other namespace will not found the secret .

```helm install rancher rancher-stable/rancher --namespace rancher --set hostname=rancher.local --set replicas=3 --set ingress.tls.source=rancher

kubectl describe certs tls-rancher-ingress -n rancher
```

```txt
Status:
  Conditions:
    Last Transition Time:        2021-07-30T07:16:03Z
    Message:                     Issuing certificate as Secret does not exist
    Observed Generation:         1
    Reason:                      DoesNotExist
    Status:                      True
    Type:                        Issuing
    Last Transition Time:        2021-07-30T07:16:03Z
    Message:                     Issuing certificate as Secret does not exist
    Observed Generation:         2
    Reason:                      DoesNotExist
    Status:                      False
    Type:                        Ready
  Next Private Key Secret Name:  tls-rancher-ingress-4fbbd
Events:                          <none>
```

changing the Rancher namespace to `cattle-system` fixed the issue

```bash
helm install rancher rancher-stable/rancher --namespace cattle-system --set hostname=rancher.local --set replicas=3 --set ingress.tls.source=rancher

kubectl describe certs tls-rancher-ingress -n cattle-system
```

```txt
Status:
  Conditions:
    Last Transition Time:  2021-08-18T12:41:17Z
    Message:               Certificate is up to date and has not expired
    Observed Generation:   1
    Reason:                Ready
    Status:                True
    Type:                  Ready
  Not After:               2021-11-16T12:41:17Z
  Not Before:              2021-08-18T12:41:17Z
  Renewal Time:            2021-10-17T12:41:17Z
  Revision:                1
Events:
  Type    Reason     Age   From          Message
  ----    ------     ----  ----          -------
  Normal  Issuing    10m   cert-manager  Existing issued Secret is not up to date for spec: [spec.dnsNames]
  Normal  Reused     10m   cert-manager  Reusing private key stored in existing Secret resource "tls-rancher-ingress"
  Normal  Requested  10m   cert-manager  Created new CertificateRequest resource "tls-rancher-ingress-rf28s"
  ```
