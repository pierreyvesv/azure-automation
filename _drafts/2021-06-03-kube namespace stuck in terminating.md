---
layout: single
title:  "deleted namespace stuck in terminating status"
date:   2021-04-30 22:40:46 +0100
categories: kubernetes
---

## deleted namespace stuck in terminating status

after reinstalling Rancher in `cattle-system`; the previous rancher namespace was not deleted. removing this namepsace was not possible and stuck in "Terminating".

last transition is
```text
    message: All content-preserving finalizers finished
    reason: ContentHasNoFinalizers
```

`kubectl edit ns rancher`

```bash
status:
  conditions:
  - lastTransitionTime: "2021-08-18T14:52:57Z"
    message: All resources successfully discovered
    reason: ResourcesDiscovered
    status: "False"
    type: NamespaceDeletionDiscoveryFailure
  - lastTransitionTime: "2021-08-18T14:52:57Z"
    message: All legacy kube types successfully parsed
    reason: ParsedGroupVersions
    status: "False"
    type: NamespaceDeletionGroupVersionParsingFailure
  - lastTransitionTime: "2021-08-18T14:52:57Z"
    message: All content successfully deleted, may be waiting on finalization
    reason: ContentDeleted
    status: "False"
    type: NamespaceDeletionContentFailure
  - lastTransitionTime: "2021-08-18T14:52:57Z"
    message: All content successfully removed
    reason: ContentRemoved
    status: "False"
    type: NamespaceContentRemaining
  - lastTransitionTime: "2021-08-18T14:52:57Z"
    message: All content-preserving finalizers finished
    reason: ContentHasNoFinalizers
    status: "False"
    type: NamespaceFinalizersRemaining
  phase: Terminating
  ```

```bash
kubectl patch ns rancher -p '{"metadata":{"finalizers":null}}'
namespace/rancher patched
```

source: <https://stackoverflow.com/questions/52369247/namespace-stuck-as-terminating-how-do-i-remove-it>