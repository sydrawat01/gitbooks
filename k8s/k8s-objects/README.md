---
description: Basic commands to create pods to deploying a cluster with a replica set.
---

# K8s objects

## Basic cofiguration

Let's start off with the basic configuration to create a pod in kubernetes. We'll assume that you are familiar with YAML configuration files.

```yaml
apiVersion:
kind:
metadata:
spec: 
```

### `apiVersion`

The `apiVersion` has only two types:

* `v1`
* `apps/v1`

The `apiVersion` are specific to the kind of the `pod` that we create.

### `kind`

The `kind` of the pod determines what kind of kubernetes pod we create, which works in conjunction with the `apiVersion`. There are 4 kinds of kubernetes pods:

* `Pod` (apiversion: `v1`)
* `Service` (apiversion: `v1`)
* `ReplicaSet` (apiversion: `apps/v1`)
* `Deployment` (apiversion: `apps/v1`)

### `metadata`

The `metadata` is used to define the name of the kubernetes service we create. It also has additional properties like labels, which are used to define tag which help us group particular type of pods during container orchestration in kubernetes. We'll get to the exact usecase of labels in a bit.

Two main properties we'll be using with metadata:

* `name`
* `labels`

### `spec`

The `spec` is the most important definition in the kubernetes config file. This defines what image the containers will be using, and how to orchestrate and deploy a set of similar pods in case of autoscaling.

Although spec has many properties, let's just start off with the basic ones:

* `containers`
  * `name`
  * `image`

