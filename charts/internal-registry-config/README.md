# Internal Registry Config
This chart sets up the internal platform registry with persistent storage via object storage. This is useful for keeping images on the platform without needing to install an additional image registry. In addition, the registry will be exposed so the platform, and external consumers can access it.

## Required Values
The following values are required to ensure a successful rollout:
```yaml
---
# What bucket storage class to use
bucketClass: ocs-storagecluster-ceph-rgw
# If the service CA should be used
useServiceCA: true

# What image to use for the configuration job
openshiftToolsImage: 'quay.io/openshift-release-dev/ocp-v4.0-art-dev@sha256:535ce24b5f1894d2a07bfa7eed7ad028ffde0659693f2a571ac4712a21cd028c'

# What resource requests and limits should be used
resources:
  requests:
    memory: 8Gi
    cpu: '4'
  limits:
    memory: 8Gi
    cpu: '4'
```

## Deployment
This chart should be deployed through the declarative state management service, as components rely on the sync wave function of the service.

Use the [meta-chart](../internal-registry-config-app/README.md) to load this into the declarative state management service.
