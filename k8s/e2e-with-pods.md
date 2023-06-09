# E2E with PODs

## Deploying multi-tier app on k8s cluster w/ minikube

We will be deploying a multi-tier voting application in a kubernetes cluster using PODs.

\[multi-tier app img]

## Configurations

### Creating the PODs

{% code title="voting-app-pod.yaml" %}
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: voting-app-pod
  labels:
    name: voting-app-pod
    app: demo-voting-app
spec:
  containers:
    - name: voting-app
      image: kodekloud/examplevotingapp_vote:v1
      ports:
        - containerPort: 80
```
{% endcode %}

{% code title="result-app-pod.yaml" %}
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: result-app-pod
  labels:
    name: result-app-pod
    app: demo-voting-app
spec:
  containers:
    - name: result-app
      image: kodekloud/examplevotingapp_result:v1
      ports:
        - containerPort: 80
```
{% endcode %}

{% code title="redis-pod.yaml" %}
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: redis-pod
  labels:
    name: redis-pod
    app: demo-voting-app
spec:
  containers:
    - name: redis
      image: redis
      ports:
        - containerPort: 6379
```
{% endcode %}

{% code title="postgres-pod.yaml" %}
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: postgres-pod
  labels:
    name: postgres-pod
    app: demo-voting-app
spec:
  containers:
    - name: postgres
      image: postgres
      ports:
        - containerPort: 5432
      env: # these should be hidden
        - name: POSTGRES_USER
          value: "postgres"
        - name: POSTGRES_PASSWORD
          value: "postgres"
```
{% endcode %}

### Creating the Services

{% code title="redis-service.yaml" %}
```yaml
apiVersion: v1
kind: Service
metadata:
  name: redis
  labels:
    name: redis-service
    app: demo-voting-app
spec:
  ports:
    - port: 6379
      targetPort: 6379
  selector:
    name: redis-pod
    app: demo-voting-app
```
{% endcode %}

{% code title="postgres-service.yaml" %}
```yaml
apiVersion: v1
kind: Service
metadata:
  name: db
  labels:
    name: postgres-service
    app: demo-voting-app
spec:
  ports:
    - port: 5432
      targetPort: 5432
  selector:
    name: postgres-pod
    app: demo-voting-app
```
{% endcode %}

{% code title="voting-app-service.yaml" %}
```yaml
apiVersion: v1
kind: Service
metadata:
  name: voting-service
  labels:
    name: voting-service
    app: demo-voting-app
spec:
  type: NodePort
  ports:
    - port: 80
      targetPort: 80
      nodePort: 30004
  selector:
    name: voting-app-pod
    app: demo-voting-app
```
{% endcode %}

{% code title="result-app-service.yaml" %}
```yaml
apiVersion: v1
kind: Service
metadata:
  name: result-service
  labels:
    name: result-service
    app: demo-voting-app
spec:
  type: NodePort
  ports:
    - port: 80
      targetPort: 80
      nodePort: 30005
  selector:
    name: result-app-pod
    app: demo-voting-app
```
{% endcode %}

### Creating the Worker POD

{% code title="worker-app-pod.yaml" %}
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: worker-app-pod
  labels:
    name: worker-app-pod
    app: demo-voting-app
spec:
  containers:
    - name: worker-app
      image: kodekloud/examplevotingapp_worker:v1
```
{% endcode %}

## Creating PODs and Services

```bash
# create voting app front-end pods and services
kubectl apply -f voting-app-pod.yaml
kubectl apply -f voting-app-service.yaml
# check the pods and services
kubectl get pods,svc # syntax to get multiple k8s objects
# get the voting app url that is exposed by minikube
minikube service voting-service --url
# create the redis pods and services
kubectl apply -f redis-pod.yaml
kubectl apply -f redis-service.yaml
# create the postgres pods and services
kubectl apply -f postgres-pod.yaml
kubectl apply -f postgres-service.yaml
# create the worker pod
kubectl create -f worker-app-pod.yaml
# create the result app back-end pods and services
kubectl apply -f result-app-pod.yaml
kubectl apply -f result-app-service.yaml
# get the result app url that is exposed by minikube
minikube service result-service --url
# check all the pods and services are healthy and running
kubectl get pods, svc
```

We can now test our application front-end and back-end dashboards using the URLs exposed by `minikube` when we create a service for the voting and result web applications.
