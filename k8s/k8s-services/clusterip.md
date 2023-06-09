# ClusterIP

\[clusterIP img]

We may have a cluster running different pods for front-end, back-end and database (eg: Redis). So the front-end servers need to communicate with the back-end service and the back-end service needs to communicate with the database. What is the right way to establish a connection between these services or "tiers" of our application?

Since the pod IPs are not static, and these pods any go down any time based on load balancing, we cannot rely on these internal IPs for communication between different services of our application. Which pod in front-end needs to connect with which pod in back-end service, and who makes these decisions? A kubernetes services helps us group such pods together and provide a single interface to access the pods in a group.

This type of service is known as ClusterIP.

## Configuration

{% code title="clusterIP.yaml" %}
```yaml
apiVersion: v1
kind: Service
metadata:
  name: clusterip-webservice
  labels:
    service: api
    tier: backend
spec:
  type: ClusterIP
  ports:
    - port: 80
      targetPort: 80
  selector:
    tier: backend
    service: api
```
{% endcode %}

## Working with ClusterIP

### Create a ClusterIP service

```bash
kubectl create -f clusterip.yaml
# OR
kubectl apply -f clusterip.yaml
# check the creation
kubectl get svc clusterip-webservice
```

### Details

```bash
kubectl describe svc clusterip-webservice
# OR
kubectl describe -f clusterip.yaml
```
