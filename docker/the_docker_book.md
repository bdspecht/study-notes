# The Docker Book: Containerization is the new Virtualization
## Introduction
### Recent container technologies include:
* OpenVZ
* Solaris zones
* Linux Containers like LXC

### Containers do not require an emulation layer or hypervisor
* Uses the operating systems normal system call interface
* Can't run Windows container on a Linux OS for this reason

### Docker is an open-source engine that automates the deployment of applications
into containers.
* Adds an application deployment engine on top of virtualized container
execution environment.
* Encourages service oriented architecture
* Each container should run a single application

### Docker is a client-server application
* Client talks to the server or daemon which then does all of the work

Docker images are essentially source code for your containers

Docker stores images in private or public repositories called registries

### Docker in stacks and clusters
* Compose - Stacks of containers to represent application stacks
* Swarm - Allows you to create clusters of containers

### Docker use cases
* Make your local development and build workflow faster, more efficient, and
more lightweight

### Docker includes
* Libcontainer - Linux container format
* Kernel namespaces - Isolation of file systems, processes, and networks
    + Each container has its own root filesystem
    + Each container has its own process environment
    + "" "" Virtual interfaces and IP addresing between containers
    + Resources are allocated invidually using cgroups
* Copy-on-write filesystem
    + Layered, fast, and require limited disk usage
* Logging
* Interactive shell

### Installing Docker
* User interfaces
    + Shipyard - Containers, images, hosts, and more
    + Portainer - Remote API
    + Kitematic - GUI for Windows and OS X

### Getting started with Docker
