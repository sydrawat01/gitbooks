# K8s networking

## Networking 101

\[networking img]

We will start with a single node kubernetes cluster. The node has an IP address, say it is 192.168.1.2 in this case. This is the IP address we use to access the kubernetes node, SSH into it etc. On a side note, remember if you are using a MiniKube setup, then I am talking about the IP address of the minikube virtual machine inside your Hypervisor. Your laptop may be having a different IP like 192.168.1.10. So its important to understand how VMs are setup.

So on the single node kubernetes cluster we have created a Single pod. As you know a pod hosts a container. Unlike in the docker world were an IP address is always assigned to a Docker container, in Kubernetes the IP address is assigned to a pod. Each pod in kubernetes gets its own internal IP Address. In this case its in the range 10.244 series and the IP assigned to the pod is 10.244.0.2. So how is it getting this IP address? When Kubernetes is initially configured it creates an internal private network with the address 10.244.0.0 and all pods are attached to it. When you deploy multiple pods, they all get a separate IP assigned. The pods can communicate to each other through this IP. But accessing other pods using this internal IP address may not be a good idea as its subject to change when pods are recreated.

## Cluster networking

\[cluster networking img]

Suppose we have two nodes running kubernetes and they have IP addresses 192.168.1.2 and 192.168.1.3 assigned to them. Note that they are not part of the same cluster yet. Each of them has a single pod deployed. As discussed in the previous slide these pods are attached to an internal network and they have their own IP addresses assigned. However, if you look at the network addresses, you can see that they are the same. The two networks have an address 10.244.0.0 and the pods deployed have the same address too.

This is not going to work well when the nodes are part of the same cluster. The pods have the same IP addresses assigned to them and that will lead to IP conflicts in the network. Now that’s one problem. When a kubernetes cluster is setup, kubernetes does not automatically setup any kind of networking to handle these issues. As a matter of fact, kubernetes expects us to setup networking to meet certain fundamental requirements. Some of these are that all the containers or pods in a kubernetes cluster must be able to communicate with one another without having to configure NAT. All nodes must be able to communicate with containers and all containers must be able to communicate with the nodes in the cluster. Kubernetes expects us to set up a networking solution that meets these criterias.

Fortunately, we don’t have to set it up all on our own as there are multiple pre-built solutions available. Some of them are the Cisco ACI networks, Cilium, Big Cloud Fabric, Flannel, Vmware NSX-t and Calico. Depending on the platform we are deploying our Kubernetes cluster on we may use any of these solutions. For example, if we were setting up a kubernetes cluster from scratch on our own systems, we may use any of these solutions like Calico, Flannel etc. If we were deploying on a Vmware environment NSX-T may be a good option. If we look at the play-with-k8s labs they use WeaveNet.

## Calico Networking Setup

We'll be using Calico to setup networking in our kubernetes environment. It is rather easy when we are setting up a k8s cluster initially. Assuming we have this setup done, Calico now manages the networks and IPs in my nodes and assigns a different network address for each network in the nodes. This creates a virtual network of all PODs and nodes were they are all assigned a unique IP Address. And by using simple routing techniques the cluster networking enables communication between the different PODs or Nodes to meet the networking requirements of kubernetes. Thus all PODs can now communicate to each other using the assigned IP addresses.
