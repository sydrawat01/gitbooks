# Kubernetes Architecture

When you deploy Kubernetes, you get a cluster.

A Kubernetes cluster consists of a set of worker machines, called [nodes](https://kubernetes.io/docs/concepts/architecture/nodes/), that run containerized applications. Every cluster has at least one worker node.

The worker node(s) host the [Pods](https://kubernetes.io/docs/concepts/workloads/pods/) that are the components of the application workload. The [control plane](https://kubernetes.io/docs/reference/glossary/?all=true#term-control-plane) manages the worker nodes and the Pods in the cluster. In production environments, the control plane usually runs across multiple computers and a cluster usually runs multiple nodes, providing fault-tolerance and high availability.

\[img from kubernetes documentation: [https://kubernetes.io/docs/concepts/overview/components/](https://kubernetes.io/docs/concepts/overview/components/)]

## Nodes(minions)

\[node-img]

A `node` is a machine – physical or virtual – on which kubernetes is installed. A `node` is a worker machine and this is were containers will be launched by kubernetes. `Nodes` were also known as `Minions` in the past. So you might here these terms used inter changeably.

What if the node on which our application is running fails? Well, obviously our application goes down. So we need to have more than one nodes.

## Cluster

\[cluster-img]

A `cluster` is a set of nodes grouped together. This way even if one node fails we have our application still accessible from the other nodes. Moreover having multiple nodes helps in sharing load as well.

## Master

\[master-img]

Now we have a cluster, but who is responsible for managing the cluster? Were is the information about the members of the cluster stored? How are the nodes monitored? When a node fails how do you move the workload of the failed node to another worker node? That’s were the `Master` comes in. The master is another node with Kubernetes installed in it, and is configured as a `Master`. The `master` watches over the nodes in the cluster and is responsible for the actual orchestration of containers on the worker nodes.

## Control Plane Components

\[ctrl plane components]

The control plane's components make global decisions about the cluster (for example, scheduling), as well as detecting and responding to cluster events (for example, starting up a new [pod](https://kubernetes.io/docs/concepts/workloads/pods/) when a deployment's `replicas` field is unsatisfied).

### `kube-apiserver`

The API server is a component of the Kubernetes [control plane](https://kubernetes.io/docs/reference/glossary/?all=true#term-control-plane) that exposes the Kubernetes API and is the front end for the Kubernetes control plane.

The main implementation of a Kubernetes API server is [kube-apiserver](https://kubernetes.io/docs/reference/generated/kube-apiserver/). kube-apiserver is designed to scale horizontally—that is, it scales by deploying more instances. You can run several instances of kube-apiserver and balance traffic between those instancesr.

### `etcd` service

The `etcd key store` is a distributed reliable key-value store used by kubernetes to store all data used to manage the cluster. Think of it this way, when you have multiple nodes and multiple masters in your cluster, etcd stores all that information on all the nodes in the cluster in a distributed manner. `etcd` is responsible for implementing locks within the cluster to ensure there are no conflicts between the Masters.

### `kube-controller-manager`

The `controllers` are the brain behind orchestration. They are responsible for noticing and responding when nodes, containers or endpoints goes down. The controllers makes decisions to bring up new containers in such cases.

### `kube-scheduler`

The `scheduler` is responsible for distributing work or containers across multiple nodes. It looks for newly created containers and assigns them to Nodes.

There are more components in the control plane, but these are the very basic ones that we need to know in order to get a better understanding of Kubernetes.

## Node Components

Node components run on every node, maintaining running pods and providing the Kubernetes runtime environment.

### `kubelet`

An agent that runs on each [node](https://kubernetes.io/docs/concepts/architecture/nodes/) in the cluster. It makes sure that [containers](https://kubernetes.io/docs/concepts/containers/) are running in a [Pod](https://kubernetes.io/docs/concepts/workloads/pods/).

### `kube-proxy`

kube-proxy is a network proxy that runs on each [node](https://kubernetes.io/docs/concepts/architecture/nodes/) in your cluster, implementing part of the Kubernetes [Service](https://kubernetes.io/docs/concepts/services-networking/service/) concept. [kube-proxy](https://kubernetes.io/docs/reference/command-line-tools-reference/kube-proxy/) maintains network rules on nodes. These network rules allow network communication to your Pods from network sessions inside or outside of your cluster.

It uses the operating system packet filtering layer if there is one and it's available. Otherwise, kube-proxy forwards the traffic itself.

### `Container runtime`

The container runtime is the software that is responsible for running containers.

Kubernetes supports container runtimes such as [containerd](https://containerd.io/docs/), [CRI-O](https://cri-o.io/#what-is-cri-o), and any other implementation of the [Kubernetes CRI (Container Runtime Interface)](https://github.com/kubernetes/community/blob/master/contributors/devel/sig-node/container-runtime-interface.md).

## Master vs. Worker Nodes

\[img]

There are two types of servers or nodes in kubernetes: `Master` and `Worker`, which work along with a set of components that make up kubernetes. So, how does one server become a `master` and the other a `slave`?

### Worker node

* The `worker node` (or minion) as it is also known, is were the containers are hosted. This requires the `container runtime` component.

{% hint style="info" %}
Docker containers are worker nodes, and to run docker containers on a system, we need a container runtime installed. And that’s were the container runtime falls. In this case it happens to be Docker. This doesn’t HAVE to be docker, there are other container runtime alternatives available such as Rocket or CRIO.
{% endhint %}

* The `worker nodes` have the `kubelet agent` that is responsible for interacting with the master to provide health information of the worker node and carry out actions requested by the master on the worker nodes.

### Master node

* The `master server` has the `kube-apiserver` and that is what makes it a `master`.
* All the information gathered are stored in a key-value store on the `Master`. The key value store is based on the popular `etcd framework`. The master also has the `controller manager` and the `scheduler`.

## `kubectl`

The command line utility to interact with kubernetes is called `kubectl` or `kube control`. The `kube control tool` is used to deploy and manage applications on a kubernetes cluster, to get cluster information, get the status of the nodes in a cluster among many other things.

### Basic commands

Here are some basic commands for `kubectl`:

* `kubectl run`: deploy an application to the cluster
* `kubectl cluster-info`: view information about the cluster
* `kubectl get nodes`: get a list of all nodes in the cluster

{% hint style="success" %}
Examples:

```bash
kubectl run hello-minikube
```

```bash
kubectl cluster-info
```

```bash
kubectl get nodes
```
{% endhint %}

