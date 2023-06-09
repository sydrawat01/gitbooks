# Deployments

There are two basic deployment strategies:

1. Recreate
2. Rolling update

## Declarative Deployment Configuration

{% code title="deployment.yaml" %}
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: deployment-webservice
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

## Working with Deployments

### Create a Deployment

```bash
kubectl create -f deployment.yaml
# OR
kubectl apply -f deployment.yaml
# check if created
kubectl get deploy deployment-webservice
```

### Update the Deployment

```bash
# Make changes to the configuration
kubectl diff -f deployment.yaml
kubectl apply -f deployment.yaml
```

### Set a deployment image

```bash
# Imperative style
kubectl set image deployment/webAPI-dep nginx-webAPI=nginx:1.14.1
```

### Record the cause of change \[changeLog]

```bash
# This is visible in rollout history
kubectl create -f deployment.yaml --record
# OR
kubectl diff -f deployment.yaml
kubectl apply -f deployment.yaml --record
# To see rollout history under `annotations`
kubectl describe deploy deployment-webservice
# OR
kubectl dedscribe -f deployment.yaml
```

### Status of the rollout deployment

```bash
kubectl rollout status deploy/deployment-webservice
# OR
kubectl rollout status -f deployment.yaml
```

### Deployment history

```bash
kubectl rollout history deploy/deployment-webservice
# OR
kubectl rollout history -f deployment.yaml
```

### Undo a rollout

```bash
kubectl rollout undo deploy/deployment-webservice
# OR
kubectl rollout undo -f deployment-webservice
```
