# LoadBalancer

\[LoadBalancer img]

We have a 3 node cluster with IPs 192.168.1.2, 192.168.1.3 and 192.168.1.4. Our application is two tier, there is a database service and a front-end web service for users to access the application. The default service type – known as ClusterIP – makes a service, such as a redis or database service available internally within the kubernetes cluster for other applications to consume.

The next tier in my application happens to be a python based web front-end. This application connects to the backend using Service created for the redis service. To expose the application to the end users, we create another service of type NodePort. Creating a service of type NodePort exposes the application on a high end port of the Node and the users can access the application at any IP of my nodes with the port 30008.

Now, what IP do you give your end users to access your application? You cannot give them all three and let them choose one of their own. What end users really want is a single URL to access the application. For this, you will be required to setup a separate Load Balancer VM in your environment. In this case I deploy a new VM for load balancer purposes and configure it to forward requests that come to it to any of the IPs of the Kubernetes nodes. I will then configure my organizations DNS to point to this load balancer when a user hosts http://myapp.com. Now setting up that load balancer by myself is a tedious task, and I might have to do that in my local or on- premise environment. However, if I happen to be on a supported CloudPlatform, like Google Cloud Platform, I could leverage the native load balancing functionalities of the cloud platform to set this up. Again you don’t have to set that up manually, Kubernetes sets it up for you. Kubernetes has built-in integration with supported cloud platforms.

## Configuration

{% code title="loadbalancer.yaml" %}
```yaml
apiVersion: v1
kind: Service
metadata:
  name: loadbalancer-webservice
  labels:
    service: api
    tier: backend
spec:
  type: LoadBalancer
  ports:
    - port: 80
      targetPort: 80
  selector:
    tier: backend
    service: api
```
{% endcode %}

## Working with LoadBalancer

### Create a LoadBalancer service

```bash
kubectl create -f loadbalancer.yaml
# OR
kubectl apply -f loadbalancer.yaml
# check the creation
kubectl get svc loadbalancer-webservice
```

### Details

```bash
kubectl describe svc loadbalancer-webservice
# OR
kubectl describe -f loadbalancer.yaml
```
