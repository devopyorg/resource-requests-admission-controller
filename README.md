![build](https://travis-ci.com/devopyio/resource-requests-admission-controller.svg?branch=master)
[![Go Report Card](https://goreportcard.com/badge/github.com/devopyio/resource-requests-admission-controller)](https://goreportcard.com/report/github.com/devopyio/resource-requests-admission-controller)
[![Docker Repository on Quay](https://quay.io/repository/devopyio/resource-requests-admission-controller/status "Docker Repository on Quay")](https://quay.io/repository/devopyio/resource-requests-admission-controller)

# Resource Requests Admission Controller

This application provides a global limit for Pod resources.

You can specify a `config.yaml` with max CPU, Memory or PVC limit and all resources exceeding the limit will be rejected.

Note that once you use it with Deployments, users must specify resource requests to `0` and resource limits to value up to `maxCPULimit` or `maxMemLimit`. Otherwise you will get a validation error.

Also this allows a custom config per namespace.

Here is an example config:
```
apiVersion: v1
kind: ConfigMap
metadata:
  name: resource-requests-controller
  namespace: kube-system
data:
  config.yaml: |-
    maxCPULimit: 2
    maxMemLimit: 2Gi
    maxPVCSize: 50Gi
    maxCPURequest: 1
    maxMemRequest: 2Gi
    customNamespaces:
      kube-system:
        # maxMemLimit and maxPVCSize is taken from top level declaration
        maxCPULimit: 1
      monitoring:
        # maxCPULimit and maxPVCSize is taken from top level declaration
        maxMemLimit: 1Gi
      default:
        # everything is unlimited.
        unlimited: true
      test-namespace:
        # everything is custom.
        unlimited: false
        maxCPULimit: 1
        maxMemLimit: 1Gi
        maxPVCSize: 10Gi
    customNames:
      {name: deployment-name, namespace: test-namespace}:
        maxPVCSize: 15Gi
        maxMemLimit: 5Gi
        maxCPULimit: 2
```

# Deployment

You can find Kubernetes Manifest in [docs](https://github.com/devopyio/resource-requests-admission-controller/blob/master/docs/deployment.yaml) directory.

Also you need to create `ValidatingWebhookConfiguration` kubernetes object. You can find an expample in [docs](https://github.com/devopyio/resource-requests-admission-controller/blob/master/docs/webhook.yaml) directory.

In order to generate `caBundle` we suggest you use [ca-bundle.sh](https://github.com/devopyio/resource-requests-admission-controller/blob/master/ca-bundle.sh) shell script.

#
