---
description: An intro to containers and why do we need them.
---

# Containers Overview

## Why do you need containers?

Let me start by sharing how I got introduced to `Docker`. In one of my previous projects, I had this requirement to setup an end-to-end stack including various different technologies like a Web Server using `NodeJS` and a database such as `MongoDB/CouchDB`, messaging system like `Redis` and an orchestration tool like `Ansible`. We had a lot of issues developing this application with all these different components. First, their compatibility with the underlying OS. We had to ensure that all these different services were compatible with the version of the OS we were planning to use. There have been times when certain version of these services were not compatible with the OS, and we have had to go back and look for another OS that was compatible with all of these different services.

Secondly, we had to check the compatibility between these services and the libraries and dependencies on the OS. We have had issues were one service requires one version of a dependent library whereas another service required another version.

The architecture of our application changed over time, we have had to upgrade to newer versions of these components, or change the database etc., and every time something changed we had to go through the same process of checking compatibility between these various components and the underlying infrastructure. This compatibility matrix issue is usually referred to as the `Matrix from Hell`.

\[matrix-from-hell-visual-representation]

Next, every time we had a new developer on board, we found it really difficult to setup a new environment. The new developers had to follow a large set of instructions and run 100s of commands to finally setup their environments. They had to make sure they were using the right Operating System, the right versions of each of these components and each developer had to set all that up by himself each time.

We also had different development test and production environments. One developer may be comfortable using one OS, and the others may be using another one and so we couldn’t guarantee the application that we were building would run the same way in different environments. And so all of this made our life in developing, building and shipping the application really difficult.

I needed something that could help us with the compatibility issues and something that would allow us to modify or change these components without affecting the other components and even modify the underlying operating systems as required. This search landed me on `Docker`.&#x20;

\[what-can-containers-do-visual-representation]

With Docker I was able to run each component in a separate container – with its own libraries and its own dependencies. All on the same VM and the OS, but within separate environments or containers. We just had to build the docker configuration once, and all our developers could now get started with a simple `“docker run”` command. Irrespective of what underlying OS they run, all they needed to do was to make sure they had `Docker` installed on their systems.
