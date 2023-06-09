# Container Orchestration

We now have our containers and our application packaged into a docker container. But what next? How do you run it in production? What if your application relies on other containers such as database or messaging services or other back-end services? What if the number of users increase and you need to scale your application? You would also like to scale down when the load decreases.

To enable these functionalities you need an underlying platform with a set of resources. The platform needs to orchestrate the connectivity between the containers and automatically scale up or down based on the load. This whole process of automatically deploying and managing containers is known as Container Orchestration.

## Kubernetes

Kubernetes is a Container Orchestration technology used to orchestrate the deployment and management of 100s and 1000s of containers in a clustered environment. We will take a deeper look at the architecture and various concepts within kubernetes.

## Kubernetes advantage

There are various advantages of container orchestration. Your application is now highly available as hardware failures do not bring your application down because you have multiple instances of your application running on different nodes. The user traffic is load balanced across the various containers. When demand increases, deploy more instances of the application seamlessly and within a matter of second and we have the ability to do that at a service level. When we run out of hardware resources, scale the number of nodes up/down without having to take down the application. And do all of these easily with a set of declarative object configuration files.
