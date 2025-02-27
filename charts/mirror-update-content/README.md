# Mirror Update Content
This chart is designed to deploy a job and supporting resources to run a job to sync update content to a local registry, allowing for caching of update content when WAN/internet connectivity is limited or unreliable.

## Required Values
The following values are required to ensure a successful job run:
```yaml
---
# Specify what platform/operators/images to sync
# Refer to the oc-mirror docs for more information on how to specify what to sync: https://docs.openshift.com/container-platform/4.16/installing/disconnected_install/about-installing-oc-mirror-v2.html
# This will be applied as-is under the .mirror key, and fed to the oc-mirror job
whatToMirror:
  platform:
    graph: false
    channels:
      - name: stable-4.17
        type: ocp
        shortestPath: true
        minVersion: 4.17.16

# To go from 4.16 --> 4.17, use the following configuration:
# whatToMirror:
#   platform:
#     graph: false
#     channels:
#       - name: stable-4.17
#         type: ocp
#         shortestPath: true
#       - name: stable-4.16
#         type: ocp
#         shortestPath: true
#         minVersion: 4.16.5

# To also mirror operator content, use the following configuration:
# whatToMirror:
#   platform:
#     graph: false
#     channels:
#       - name: stable-4.17
#         type: ocp
#         shortestPath: true
#         minVersion: 4.17.16
#   operators:
#     - catalog: registry.redhat.io/redhat/redhat-operator-index:v4.17
#       packages:
#        - name: ansible-automation-platform
#        - name: openshift-cert-manager-operator
#        - name: kubernetes-nmstate-operator
#        - name: kubevirt-hyperconverged
#        - name: odf-operator
#        - name: openshift-pipelines-operator-rh

# To also mirror additional images, use the following configuration:
# whatToMirror:
#   platform:
#     graph: false
#     channels:
#       - name: stable-4.17
#         type: ocp
#         shortestPath: true
#         minVersion: 4.17.16
#   operators:
#     - catalog: registry.redhat.io/redhat/redhat-operator-index:v4.17
#       packages:
#        - name: ansible-automation-platform
#        - name: openshift-cert-manager-operator
#        - name: kubernetes-nmstate-operator
#        - name: kubevirt-hyperconverged
#        - name: odf-operator
#        - name: openshift-pipelines-operator-rh
#   additionalImages:
#    - name: registry.redhat.io/ubi8/ubi:latest
#    - name: registry.redhat.io/ubi9/ubi@sha256:20f695d2a91352d4eaa25107535126727b5945bff38ed36a3e59590f495046f0

# Where to mirror the content to
destinationRegistry:
  # Hostname of the registry with optional path to a specific repo
  host: default-route-openshift-image-registry.apps.yourcluster.com/mirror-update-content
  # If certificates should be validated
  skipTLS: 'false'
  # If this is the internal OCP regisry
  internalRegistry: true

# Tools image to use for the job
openshiftToolsImage: 'quay.io/openshift-release-dev/ocp-v4.0-art-dev@sha256:535ce24b5f1894d2a07bfa7eed7ad028ffde0659693f2a571ac4712a21cd028c'

# Base64-encoded pull secret
pullSecret: 'base64-encoded-pull-secret'
```

## Deployment
This chart can be deployed directly using Helm, or optionally, via the "meta" chart which creates an application in the declarative state management (ArgoCD) service to deploy and maintain it.

To deploy it directly with helm, run the following:
```
helm install \
-f /path/to/values.yaml \
--namespace mirror-update-content \
mirror-update-content \
charts/mirror-update-content/ \
--create-namespace
```

Use the [meta-chart](../mirror-update-content-app/README.md) to load this into the declarative state management (ArgoCD) service.
