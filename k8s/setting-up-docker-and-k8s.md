---
description: >-
  This page tells you how to setup a local dev env in MacOS to run the k8s
  service.
---

# Setting up Docker and k8s

{% hint style="info" %}
We will be using `homebrew` to install almost everything, so make sure you have `homebrew` installed on your MacOS.
{% endhint %}

## Install homebrew

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

To know more about `homebrew` and what it does, visit their [website](https://brew.sh/).

## Install Docker

Once we have `homebrew` setup, we will install `Docker` as our primary containerization tool.

```bash
brew install docker
```

## Enable `kubectl` via Docker

The `kubectl` command line tool comes bundled with Docker, so we just need to enable it in the Docker desktop via Settings.

\[img-docker-kubectl]

## Install `minikube`

Now we will install `minikube`, which is a lightweight, single-nore Kubernetes clister that can be run locally on your dev machine. This will allow us to test and develop kubernetes applications without having to set up a full scale cluster.

```bash
brew install minikube
```

To start the minikube cluster, run:

```bash
minikube start
```

This will setup the cluster and connect to the minikube namespace.

{% hint style="info" %}
In case you get an error:\
[`Kubernetes error: Unable to connect to the server: dial tcp 127.0.0.1:8080`](https://stackoverflow.com/questions/54012973/kubernetes-error-unable-to-connect-to-the-server-dial-tcp-127-0-0-18080)\


You can try changing the kubectl endpoint to another context:

```bash
kubectl config get-contexts
kubectl config use-context docker-desktop # OR minikube
```

In case this does not solve your problem, it might be the case that you need to point kubectl to the minikube context. We can do that by simply running the `minikube start` command.

If the kubectl is pointing to the correct context, the following command will execute without errors giving you the information about the k8s control plane cluster:

```bash
kubectl cluster-info
```
{% endhint %}

That's about it! These are some of the basic things we need to setup Docker and k8s on our local environment.
