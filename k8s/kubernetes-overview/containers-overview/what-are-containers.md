# What are containers?

## Containers

\[diagram-of-containers]

Containers are completely isolated environments, as in they can have their own processes or services, their own network interfaces, their own mounts, just like Virtual machines, except that they all share the same OS kernel. We will look at what that means in a bit. But its also important to note that containers are not new with `Docker`. Containers have existed for about 10 years now and some of the different types of containers are `LXC`, `LXD` , `LXCFS` etc. `Docker` utilizes `LXC` containers. Setting up these container environments is hard as they are very low level and that is were `Docker` offers a high-level tool with several powerful functionalities making it really easy for end users like us.

## Docker

To understand how Docker works let us revisit some basics concepts of Operating Systems first. If we look at operating systems like `Ubuntu`, `Fedora`, `Suse` or `Centos` – they all consist of two things. An `OS Kernel` and a set of software. The `OS Kernel` is responsible for interacting with the underlying hardware. While the OS kernel remains the same– which is `Linux` in this case, it’s the software above it that make these Operating Systems different. So, we have a common `Linux kernel` shared across all operating systems and some custom software that differentiate them from each other. We wont be able to run a windows based container on a Docker host with Linux OS on it. For that we would require docker on a windows server.

Not being able to run another kernel on the OS might look like a disadvantage, but it isn't! Because unlike `Hypervisors`, Docker is not meant to virtualize and run different operating systems and kernels on the same hardware. The main purpose of Docker is to containerize the applications and to ship and run them.

### VMs vs. Containers

\[diagram-listing-out-differences]

It is important to note here that Docker has less isolation and more resources shared between containers (like the kernel), whereas VMs have complete isolation from each other. Since VMs do not rely on the underlying OS or kernel, we can run different types of OS on the same hypervisor.

### Image

\
An image is a template or a package, just like a VM template, that is used to create one or more containers. Containers are running instances of images that are isolated and have their own environments and set of processes.

## Advantages

If you look at it, traditionally developers developed applications. Then they hand it over to Ops team to deploy and manage it in production environments. They do that by providing a set of instructions such as information about how the hosts must be setup, what prerequisites are to be installed on the host and how the dependencies are to be configured etc. Since the Ops team did not develop the application on their own, they struggle with setting it up. When they hit an issue, they work with the developers to resolve it.

With Docker, a major portion of work involved in setting up the infrastructure is now in the hands of the developers in the form of a Docker file. The guide that the developers built previously to setup the infrastructure can now easily put together into a `Dockerfile` to create an image for their applications. This image can now run on any container platform and is guaranteed to run the same way everywhere. So the Ops team now can simply use the image to deploy the application. Since the image was already working when the developer built it and operations are not modifying it, it continues to work the same when deployed in production.
