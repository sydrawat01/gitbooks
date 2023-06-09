# K8s services

Kubernetes Services enable communication between various components within and outside of the application. Kubernetes Services helps us connect applications together with other applications or users. For example, our application has groups of pods running various sections, such as a group for serving front-end load to users, another group running back-end processes, and a third group connecting to an external data source. It is Services that enable connectivity between these groups of pods. Services enable the front-end application to be made available to users, it helps communication between back-end and front-end pods, and helps in establishing connectivity to an external data source. Thus services enable loose coupling between microservices in our application.

## Use case

So we deployed our pod having a web application running on it. How do we, as an external user access the web page? First of all, let's look at the existing setup. The Kubernetes Node has an IP address and that is 192.168.1.2. My laptop is on the same network as well, so it has an IP address 192.168.1.10. The internal pod network is in the range 10.244.0.0 and the pod has an IP 10.244.0.2. Clearly, I cannot ping or access the pod at address 10.244.0.2 as its in a separate network. So what are the options to see the webpage?

First, if we were to SSH into the kubernetes node at 192.168.1.2, from the node, we would be able to access the pod’s webpage by doing a curl or if the node has a GUI, we could fire up a browser and see the webpage in a browser following the address http://10.244.0.2. But this is from inside the kubernetes Node and that’s not what I really want. I want to be able to access the web server from my own laptop without having to SSH into the node and simply by accessing the IP of the kubernetes node. So we need something in the middle to help us map requests to the node from our laptop through the node to the pod running the web container.

That is where the kubernetes service comes into play. The kubernetes service is an object just like pods, Replicaset or Deployments that we worked with before. One of its use case is to listen to a port on the Node and forward requests on that port to a port on the pod running the web application. This type of service is known as a NodePort service because the service listens to a port on the Node and forwards requests to pods.

## Types of Services

There are three main types of services:

1. `NodePort`\
   The service makes an internal POD accessible on a Port on the Node.
2. `ClusterIP`\
   The service creates a virtual IP inside the cluster to enable communication between different services such as a set of front-end servers to a set of backend- servers.
3. `LoadBalancer`\
   It provisions a load balancer for our service in supported cloud providers. A good example of that would be to distribute load across different web servers.

Let's discuss them in further detail and their configurations.
