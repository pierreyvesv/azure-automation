---
layout: single
title:  "kubernetes cheatsheet"
date:   2021-04-30 22:40:46 +0100
categories: kubernetes
---

https://kubernetes.io/docs/reference/kubectl/cheatsheet/

### permanently save the namespace for all subsequent kubectl commands in that context.

`kubectl config set-context --current --namespace=ggckad-s2`

use "all" to see everything

`kubectl config set-context --current --namespace=all`


### list all container on a cluster

`kubectl get pods --all-namespaces -o=jsonpath='{range .items[*]}{"\n"}{.metadata.name}{":\t"}{range .spec.containers[*]}{.image}{", "}{end}{end}'`

### AKS

az aks get-credentials --resource-group azure-kubernetes1 --name kubernetes1

az aks browse --resource-group azure-kubernetes1 --name kubernetes1

### scaling
kubectl scale deployments webserver --replicas=5
kubectl get pods


### 
kubectl create secret generic secrets-store-creds --from-literal clientid=d8e5e261-447a-41b0-994c-9f9ea133fa9e --from-literal clientsecret=B2S6wHTQA-O53Nt.Wk4Qmgona-p0TP5t

filter pods by label
`kubectl --namespace=default get pods -l "app=secrets-store-csi-driver"`


export POD
```bash
export POD_NAME=$(kubectl get pods --namespace monitoring -l "app=prometheus,component=pushgateway" -o jsonpath="{.items[0].metadata.name}")
kubectl --namespace monitoring port-forward $POD_NAME 9091
```


### Renew a certificat

```bash
# renew certificat

kubectl get certs -n rancher --no-headers=true | awk '{print $1}' | xargs -n 1 kubectl -n rancher patch certificate --patch '
- op: replace
  path: /spec/renewBefore
  value: 1h
' --type=json

  kubectl get certs -n rancher --no-headers=true | awk '{print $1}' | xargs -n 1 kubectl -n rancher patch certificate -n rancher --patch '
- op: remove
  path: /spec/renewBefore
' --type=json
```

```bash
kubectl describe certs tls-rancher-ingress -n rancher
==>
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

```bash
kubectl describe certs tls-rancher-ingress -n cattle-system

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
  Normal  Issuing    10m   cert-manager  The certificate has been successfully issued
```

### watch

`watch kubectl get scv,ing,pods`

### port forward to the first pods

Example:
`kubectl --namespace logger port-forward $(kubectl --namespace logger get pod -l service=kibana -o template --template="{{(index .items 0).metadata.name}}") 5601:5601`

### get Argocd default admin password

`kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d`

## delete all pending failing jobs

`kubectl delete jobs --field-selector status.successful=0`

## force cronjob run

`k create job --from=cronjob/<cronjob_name> name-manual-001`

## kubectl create configmap

`kubectl create configmap one-test --from-file=./examples/simple_test.jmx`


## node ip addresses

`kubectl get nodes --selector=kubernetes.io/role!=master -o jsonpath={.items[*].status.addresses[?\(@.type==\"InternalIP\"\)].address}`

## linux command to one pod

`kubectl exec -ti $(kubectl get pod -l app=workout-gateway -o jsonpath="{.items[0].metadata.name}") top`

`kubectl exec -ti $(kubectl get pod -l app=workout-gateway -o jsonpath="{.items[0].metadata.name}") -- ls -l /app`

## kubectl get events

```bash
kubectl get events
kubectl get events --field-selector type=Warning
kubectl get events --field-selector type=!Normal
kubectl get events --field-selector involvedObject.kind!=Pod
kubectl get events --field-selector involvedObject.kind=Node,involvedObject.name=minikube
```

## show version

`kubectl get pods --all-namespaces -o=jsonpath='{range .items[*]}{"\n"}{.metadata.name}{":\t"}{range .spec.containers[*]}{.image}{", "}{end}{end}' | sort`
