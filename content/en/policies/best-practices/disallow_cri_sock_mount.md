---
type: "docs"
title: Disallow CRI socket mounts
linkTitle: Disallow CRI socket mounts
weight: 4
description: >
    Container daemon socket bind mounts allows access to the container engine on the  node. This access can be used for privilege escalation and to manage containers  outside of Kubernetes, and hence should not be allowed.  
---

## Policy Definition
<a href="https://github.com/kyverno/policies/raw/main//best-practices/disallow_cri_sock_mount.yaml" target="-blank">/best-practices/disallow_cri_sock_mount.yaml</a>

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: disallow-container-sock-mounts
  annotations:
    policies.kyverno.io/title: Disallow CRI socket mounts
    policies.kyverno.io/category: Best Practices
    policies.kyverno.io/description: >-
      Container daemon socket bind mounts allows access to the container engine on the 
      node. This access can be used for privilege escalation and to manage containers 
      outside of Kubernetes, and hence should not be allowed.  
spec:
  validationFailureAction: audit
  rules:
  - name: validate-docker-sock-mount
    match:
      resources:
        kinds:
        - Pod
    validate:
      message: "Use of the Docker Unix socket is not allowed."
      pattern:
        spec:
          =(volumes):
            - =(hostPath):
                path: "!/var/run/docker.sock"
  - name: validate-containerd-sock-mount
    match:
      resources:
        kinds:
        - Pod
    validate:
      message: "Use of the Containerd Unix socket is not allowed."
      pattern:
        spec:
          =(volumes):
            - =(hostPath):
                path: "!/var/run/containerd.sock"
  - name: validate-crio-sock-mount
    match:
      resources:
        kinds:
        - Pod
    validate:
      message: "Use of the CRI-O Unix socket is not allowed."
      pattern:
        spec:
          =(volumes):
            - =(hostPath):
                path: "!/var/run/crio.sock"                
```
