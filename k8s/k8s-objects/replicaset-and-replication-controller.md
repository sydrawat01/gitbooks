# ReplicaSet & Replication Controller

## Declarative `ReplicationController` configuration

{% code title="replicationController.yaml" %}
```yaml
apiversion: v1
kind: ReplicationController
metadata:
  name: replicationcontroller-webservice
  labels:
    service: api
    tier: backend
spec:
  template:
    metadata:
      name: webservice
      labels:
        service: api
        tier: backend
    spec:
      containers:
        - name: nginx-webservice
          image: nginx:latest
  replicas: 3
```
{% endcode %}

The `spec` configuration object in a `ReplicationController` is the same configuration that we use to define for a `Pod`. The only new property we add here is the replicas, which represents how many `replicas` of a particular Pod are required at all times, which is specified in the `template` of the spec.

{% hint style="info" %}
`ReplicationController` objects are now deprecated, so we will be using `ReplicaSet` moving forward with newer versions of k8s.
{% endhint %}

## Declarative `ReplicaSet` configuration

{% code title="replicaSet.yaml" %}
```yaml
apiversion: apps/v1
kind: ReplicaSet
metadata:
  name: replicaset-webservice
  labels:
    service: api
    tier: backend
spec:
  template:
    metadata:
      name: webservice
      labels:
        service: api
        tier: backend
    spec:
      containers:
        - name: nginx-webservice
          image: nginx:latest
  replicas: 3
  selector:
    matchLabels:
      tier: backend
      service: api
```
{% endcode %}

Notice we use `apps/v1` as the `apiVersion` here. We then need to combine any other standalone pod that is already running the same service in a pod container based on the labels. This is where defining labels to your pods comes in handy.

Imagine we have a Pod P1 which is running an `nginx` container service for a backend application. Now we create a ReplicaSet which requires 3 replicas of the same application service. The ReplicaSet will not create 3 new Pods within the set, rather it will consume the Pod P1, and create two more Pods P2 and P3 to match the minimum requirement. This "consumption" of an exactly same Pod is done on the basis of the labels we provide at creation.

## Working with ReplicaSets

### Creating a ReplicaSet

```bash
kubectl create -f replicaSet.yaml
```

### Check creation

```bash
kubectl get replicasets
# OR
kubectl get rs
```

### Delete a ReplicaSet

```bash
kubectl delete replicaset replicaset-webservice
# OR
kubectl delete -f replicaSet.yaml
```

### Update configuration

```bash
kubectl diff -f replicaSet.yaml
kubectl apply -f replicaSet.yaml
```

### ReplicaSet details

```bash
kubectl describe -f replicaSet.yaml
# Select the replica set name you want to get detailed info about
kubectl describe replicaset replicaset-webservice
```
