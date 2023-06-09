# E2E with Deployments

The major advantage of using deployments over PODs for a multi-tier application is that we can scale-up or scale-down our application easily, make upgrades and perform rollbacks safely with their history.

## Configurations

{% code title="voting-app-deploy.yaml" %}
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: voting-app-deploy
  labels:
    name: voting-app-deploy
    app: demo-voting-app
spec:
  replicas: 1
  selector:
    matchLabels:
      name: voting-app-pod
      app: demo-voting-app
  template:
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

{% code title="redis-deploy.yaml" %}
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis-deploy
  labels:
    name: redis-deploy
    app: demo-voting-app
spec:
  replicas: 1
  selector:
    matchLabels:
      name: redis-pod
      app: demo-voting-app
  template:
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

{% code title="postgres-deploy.yaml" %}
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: postgres-deploy
  labels:
    name: postgres-deploy
    app: demo-voting-app
spec:
  replicas: 1
  selector:
    matchLabels:
      name: postgres-pod
      app: demo-voting-app
  template:
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
          env:
            - name: POSTGRES_USER
              value: "postgres"
            - name: POSTGRES_PASSWORD
              value: "postgres"
```
{% endcode %}

{% hint style="info" %}
You might run into some issues while creating the Postgres deployment/service/POD.

In case you do, try updating the Postgres image to `postgres:10.0`
{% endhint %}

{% code title="worker-app-deploy.yaml" %}
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: worker-app-deploy
  labels:
    name: worker-app-deploy
    app: demo-voting-app
spec:
  replicas: 1
  selector:
    matchLabels:
      name: worker-app-pod
      app: demo-voting-app
  template:
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

{% code title="result-app-deploy.yaml" %}
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: result-app-deploy
  labels:
    name: result-app-deploy
    app: demo-voting-app
spec:
  replicas: 1
  selector:
    matchLabels:
      name: result-app-pod
      app: demo-voting-app
  template:
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

## Creating PODs and Services

{% hint style="info" %}
We will use the same service object configurations from the previous section.
{% endhint %}

```bash
# create voting app front-end pods and services
kubectl apply -f voting-app-deploy.yaml
kubectl apply -f voting-app-service.yaml
# check the pods and services
kubectl get pods,svc # syntax to get multiple k8s objects
# get the voting app url that is exposed by minikube
minikube service voting-service --url
# create the redis pods and services
kubectl apply -f redis-deploy.yaml
kubectl apply -f redis-service.yaml
# create the postgres pods and services
kubectl apply -f postgres-deploy.yaml
kubectl apply -f postgres-service.yaml
# create the worker pod
kubectl create -f worker-app-deploy.yaml
# create the result app back-end pods and services
kubectl apply -f result-app-deploy.yaml
kubectl apply -f result-app-service.yaml
# get the result app url that is exposed by minikube
minikube service result-service --url
# check all the pods and services are healthy and running
kubectl get pods, svc
```

We can now test our application front-end and back-end dashboards using the URLs exposed by `minikube` when we create a service for the voting and result web applications.

## Scale the application

Now let's scale the application to use 3 replicas instead of 1 in the deployments for the voting-app. We will not be scaling the internal services for redis or postgres.
