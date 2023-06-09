# NodePort

\[nodeport img]

Here are a few basic terms from the point of view of the service:

1. `targetNode`\
   This port is where our application is running, i.e, the port of the pod. This is where the service forwards the request to.
2. `port`\
   This port is on the service itself.
3. `NodePort`\
   This is used to access the application running on our pod, externally. The valid range for these ports are `30000-32767`.

## Configuration

{% code title="nodeport.yaml" %}
```yaml
apiVersion: v1
kind: Service
metadata:
  name: nodeport-webservice
  labels:
    service: api
    tier: backend
spec:
  type: NodePort
  ports:
    - targetPort: 80 # Port on the pod. If not provided, it is assumed to be the same as port
      port: 80 # Port on the service
      nodePort: 30008 # Port of the node. Valid range = [30000-32767]
  selector:
    service: app
    tier: backend
```
{% endcode %}

## Working with NodePort

### Create a NodePort service

```bash
kubectl create -f nodeport.yaml
# OR
kubectl apply -f nodeport.yaml
# check the creation
kubectl get svc nodeport-webservice
```

But what to do in case of multiple pods with multiple IP addresses? This is done based on the labels we provide when we create the pod and use the same labels when creating a service. To balance the load on these pods, kubernetes uses a random algorithm. This is when similar pods are within a single node. Thus, this service acts as a built-in load balancer to distribute loads across different pods.

What happens when the pods are distributed across multiple nodes? Kubernetes does this automatically when we create a service and then we can use any IP address and port to access any pod via any node without any additional configuration changes.

To get the IP address of the worker node, we can use the following command:

```shell
# since we are using minikube for local kubernetes env:
minikube service my-app-service-name --url
```

### Details

```bash
kubectl describe svc nodeport-webservice
# OR
kubectl describe -f nodeport.yaml
```
