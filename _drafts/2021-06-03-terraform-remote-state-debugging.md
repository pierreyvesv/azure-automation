---
layout: single
title:  "Remote state manipulation"
date:   2021-04-30 22:40:46 +0100
categories: finops
---

## mannually delete resource that have been manually erased on Azure

you can also use `terraform state rm`

### download the state locally

download the remote state `terraform state pull > remote_state.out`

get the output from the downloaded state `terraform output -state=remote_state.out`

### edit the remote_state.out

remove the resource with `terraform state rm -state=remote_state.out resource_name`

### increase the serial

```json
  "version": 4,
  "terraform_version": "0.12.19",
  "serial": 446,
  "lineage": "1369ef92-2fb8-0c42-53eb-13c65ceac40c",
```

### Push back the state online

`terraform state push remote_state.out`

## moving module

when you need to edit ethe terraform remote state, you might want to use it localy

moving module requires to use terraform init

- unconfigure the remote backend tfstate ( by commenting out the backend block
- run terraform init
- terraform propose then to copy the tfstate locally 
- move the resource, try and plan
- re add the backend block for remote state
- run terraform init and specify to copy back the state