# Basic pod with k8

## Declarative Pod Configuration

Let's start with creating a k8 configuration in YAML using the core concepts from the previous page.

{% code title="pod-definition.yaml" %}
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: webservice
  labels:
    service: api
    tier: backend
spec:
  containers:
    - name: nginx-webservice
      image: nginx:latest
```
{% endcode %}

## Creating the Pod

To create the pod, we'll use the `kubectl` command line interface.

```bash
kubectl create -f pod-definition.yaml
```

### Check creation of pod

```bash
kubectl get pods
```

The above command will list out all the pods that are running in the current namespace.

{% hint style="info" %}
To check which namespace you are using, use the command:

```bash
kubectl get ns
#OR 
kubectl get namespace
```

If we have `minikube` setup in our local dev environment, and the service is running, it'll show that we are currently in the minikube namespace.
{% endhint %}

## Delete the Pod

Delete the k8s object defined in the configuration file:

```bash
kubectl delete -f pod-definition.yaml
# OR
kubectl delete pod webservice
```

## Update the Pod configuration

There are multiple ways to update the configuration of a Pod. One of them being deleting the Pod and updating the configuration file and then creating the object again. This approach is not that great, since it creates a downtime of a service hosted in the container.

We can create/patch live objects using the commands:

```bash
kubectl diff -f pod-definition.yaml
kubectl apply -f pod-definition.yaml
```

## Pod details

To get a list of running pods:

```bash
kubectl get pods
```

Use the name of the pod to get the details about a particular pod:

```bash
kubectl describe pod webservice
# OR
kubectl describe -f pod-definition.yaml
```
