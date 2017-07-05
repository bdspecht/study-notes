# The Docker Book: Containerization is the new Virtualization
## Introduction
### Recent container technologies include:
* OpenVZ
* Solaris zones
* Linux Containers like LXC

### Containers do not require an emulation layer or hypervisor
* Uses the operating systems normal system call interface
* Can't run Windows container on a Linux OS for this reason

### Docker is an open-source engine that automates the deployment of applications into containers.
* Adds an application deployment engine on top of virtualized container
execution environment.
* Encourages service oriented architecture
* Each container should run a single application

### Docker is a client-server application
* Client talks to the server or daemon which then does all of the work

* Docker images are essentially source code for your containers

* Docker stores images in private or public repositories called registries

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
Running a container:
`sudo docker run -i -t ubuntu /bin/bash`
* -i - Keeps STDIN open from the container
* -t - Tells Docker to assign a pseudo-tty to the container we're about to create

#### Images
You can use base images like the ubuntu base image or fedora, debian,centos, and etc as the basis for building your own images on the operating system of your choice
* Alpine Linux image is recommended as extremely lightweight, generally 5Mb in size for the base image

#### Container naming
Recommended to use container names to make managing your container easier.
* `sudo docker run --name XXX ...`

#### Starting a stopped container
* `docker start name/id`
* There is also `docker create` which creates a container but does not run it

#### Logging
* `docker logs`
* You can also watch the log similiar to `tail -f`
    + `docker logs --tail 10 name`

##### Docker log drivers
Since Docker 1.6 you can control the logging driver used.
* `--log-driver` option

#### Docker statistics
* Shows statistics for one or more running Docker containers.
    + `sudo docker stats XXX`

#### Running a proces inside a running container
* Additonal processes can be run by using `docker exec`

#### Automatic container restarts
* The `--restart` flag checks the container's exit code and makes a decision wether or not to restart it.
* Default behavior is to not restart containers at all
* `-on-failure` flag accepts a restart count
    + `--restart=on-failure:5`

### Working with Docker images and repositories
#### What is a docker image
* Docker image is made up of filesystems layered over each other

1. When a container has boot it is moved into memory
2. The boot filesystem unmounted to free up the RAM used by the inird disk image

* Docker takes advantage of a union mount
    + Adds read-only filesystems onto the root filesystem
    + Allows multiple filesystems to be mounted at one time, but still appears as only one

* Images can be layered on top of another. The image below is called the parent image and you can traverse each layer until you reach the base layer.

* If you want to change a file, then that file will be copied from the read-only layer below into the read-write layer. The read-only version of the file will still exist but is now hidden under a copy.
    + Called 'copy on write'

#### Building our own images
Two ways to create images
1. `docker commit` command
    + not recommended, as building with a Dockerfile is more flexible and powerful
2. `docker build` with Dockerfile

#### Creating our first Dockerfile
The directory where the Dockerfile is created, is called our build environment.
    + Docker calls this a context or build context
    + Docker will upload the build context, as well as any files and directories contained in it.
    + .dockerignore files can be used to ommit things

* Images can and should be named and tagged

#### Using the build cache for templating
You can build your Dockerfiles in the form of simple templates
* Add package repo or updating package near the top of the template to ensure the cache is hit

#### Dockerfile instructions
`CMD` - Running the command when the container is being built. Specifies the command to run when the container is launched.
    + `CMD ["/bin/bash", "-l"]

Example default command with ENTRYPOINT
```
ENTRYPOINT ["/usr/sbin/nginx"]
CMD ["-h"]
```

`VOLUME` - Allows us to add data, database, or other content into an image without commiting it to the image. We can also share data between ccontainers.

`ADD` - Adds files from outside of the context (local fs or http).
    + Docker automatically unpacks tar
    + **Build cache can be invalidated by ADD instructions

