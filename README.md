# DockerNotes
Summary of the book "DockerDeepDive" is written in this repo


| Contents|
| ---------------------- |
| 1. [**Introduction**](#introduction) |
| 2. [**The Big Picture**](#the-big-picture) |
| 3. [**Commands**](#commands) |
| 4. [**Multi-Architecture**](#multi-architecture) |
| 5. [**Containers**](#containers) |
| 6. [**Docker Compose**](#docker-compose) |

## **Introduction** ##

We used to live in a world where every time the business wanted a new application we had to buy a brand-new
server. VMware came along and enabled us to drive more value out of new and existing company IT assets. As
good as VMware and the VM model is, it’s not perfect. Following the success of VMware and hypervisors came
a newer more eﬃcient and lightweight virtualization technology called containers. But containers were initially
hard to implement and were only found in the data centers of web giants that had Linux kernel engineers on
staﬀ. Along came Docker, Inc. and suddenly containers were available to the masses.


Docker Architecture

![image](/images/docker_architecture.png)


runc: job is to interface with the underlying OS and start and stop containers. 

containerd: It manages the entire
lifecycle of a container, including pulling images, creating network interfaces, and managing lower-level runc
instances.

The Docker daemon ( dockerd ) sits above containerd and performs higher-level tasks such as; exposing the
Docker remote API, managing images, managing volumes, managing networks, and more…

Docker Swarm: Docker also has native support for managing clusters of nodes running Docker. These clusters are called swarms
and the native technology is called Docker Swarm

However, most people are choosing to use Kubernetes instead of Docker Swarm.

The Open Container Initiative (OCI) was instrumental in standardizing the container runtime format and
container image format.


Installing Docker on Linux:


Update the apt package index

Install Docker from the official repo






## **The Big Picture** ##

Images: Docker image contains an OS ﬁlesystem, an application, and all application dependencies. In the Docker world, an image is eﬀectively a stopped container. If you’re a developer, you can think of an image as a class.

If an image tag after the repository name is not specified, Docker will assume you are referring to the image tagged as latest. 

## **Commands** ##

* ```sudo docker --version```: Check if docker is installed

* ```systemctl is-active docker``` : Linux system command to check if docker is active



* Pull all of the images in a repository by adding the -a ﬂag to the ```docker image pull``` . Then ```docker image ls``` to look at the images pulled. Its probably not a good idea to pull all images as it images will be of larger size.

* ```docker image pull ubuntu:latest``` : Pull the image 


* ```docker image ls``` : Check existing images


* ```docker container run -it ubuntu:latest /bin/bash``` : Run the container. The -it ﬂags tell Docker to make the
container interactive and to attach the current shell to the container’s terminal


* ```docker container run -d  --name web1 --publish 8080:8080 test:latest``` : -d to run container in background and print container ID, name of the container and attach point to port 8080, image name to run


* ```docker container exec -it vigilant_borg bash``` : attach shell to the terminal of a running container


* ```ps -elf``` : In order to list all running processes inside of the container.


* ```docker container ls```: verify that container is still running.

* ```docker container ls -a``` : Adding -a tells Docker to list all containers, even those in the stopped state

Stop the container and kill it using below commands

* ```docker container stop vigilant_borg```

* ```docker container rm vigilant_borg```

* ```docker container inspect``` : will show you detailed conﬁguration and runtime information about a
container. It accepts container names and container IDs as its main argument.

* ```docker image build -t test:latest .``` : Build an image with tag name -t.

* ```docker image inspect ubuntu:latest``` : Another way to see the layers of an image is to inspect the image with the ```docker image inspect```


* ```docker image ls --filter dangling=true``` : --filter ﬂag to ﬁlter the list of images returned by ```docker image ls``` . 

A dangling image is an image that is no longer tagged, and appears in listings as <none>:<none> . A common way they occur is when building a new image giving it a tag that already exists. When this happens, Docker will build the new image, notice that an existing image already has the same tag, remove the tag from the existing image and give it to the new image.

* ```docker search``` : command lets you search Docker Hub from the CLI.  By default, Docker will only display 25 lines of results. However, you can use the --limit ﬂag to increase that to a maximum of 100.

* ```docker image rm``` : Deletes the image on Docker host. rm is for remove

* ```docker container rm $(docker container ls -aq) -f``` : This commands should never be executed on production

![image](/images/docker_layers.png)

* Dockerfile instructions (“ENV”, “EXPOSE”, “CMD”, and “ENTRYPOINT”) add metadata to the image and do not result in permanent layers being created.

* Docker Images are stored in centralised place called image registries.

* Image registries contain one or more image repositories. In turn, image repositories contain one or more images. 


![image](/images/image_registry.png)

* Docker Hub has the concept of oﬃcial repositories and unoﬃcial repositories. As the name suggests, oﬃcial repositories are the home to images that have been vetted and curated by Docker,
Inc. Unoﬃcial repositories can be like the wild-west — you should not assume they are safe, well-documented or built
according to best practices

## **Multi-architecture** ##

```docker buildx``` is a Docker CLI plugin that extends the Docker CLI to support multi-arch builds.


The manifest list is exactly what it sounds like: a list of architectures supported by a particular image tag. Each supported architecture then has its own *manifest detailing the layers that make it up.

On the left is the manifest list with entries for each architecture the image supports. The arrows show that each entry in the manifest list points to a manifest containing image conﬁg and layer data

![image](/images/manifest.png)

When you pull an image, your Docker client makes the relevant calls to the Docker Registry API exposed by Docker Hub. If a manifest list exists for the image, it will be parsed to see if an entry exists for Linux on ARM. If an ARM entry exists, the manifest for that image is retrieved and parsed for the crypto ID’s of the layers that make up the
image. Each layer is then pulled from Docker Hub.

```docker manifest inspect golang | grep 'architecture\|os'```: The ```docker manifest``` command lets you inspect the manifest list of any image on Docker Hub

## **Containers** ##

* A container is the runtime instance of an image.

![image](/images/container.png)



* It's always a good idea to run container with restart policy. There are 3-types

````
* always
* unless stopped
* on-failed
````



``` docker container run --name neversaydie -it --restart always alpine sh```

* The process of taking an application and conﬁguring it to run as a container is called **containerizing**.


* The process of containerizing an app looks like this:
```
Start with your application code and dependencies
Create a Dockerfile that describes your app, its dependencies, and how to run it
Feed the Dockerfile into the docker image build command
Push the new image to a registry (optional)
Run container from the image
```

![image](/images/containerizing.png)

* Multi-stage builds can be to build and ship smaller images to our production environments. This is by combining multiple dockerfiles using ```FROM```


## **Docker Compose** ##

* Docker Compose, which deploys and manages multi-container applications on Docker nodes running in single-engine mode.


* Docker compose comes to picture in deploying and managing lots of small microservices

* When new changes are made to Dockerfile, instead of rebuilding the docker file to affect changes in docker-compose. One can physically replace the files in location mentioned in docker-compose

* docker-compose ﬁle has 4 top-level keys:
```
version
services
networks
volumes
```

```docker-compose --version``` : To check if docker-compose is installed



```docker-compose up``` : bring up the compose service


```docker-compose -f prod-equus-bass.yml up```: If  Compose
ﬁle has a diﬀerent name, we need to specify it with the -f ﬂag. USual name will be ``docker-compose.yml``. At the last ``&`` and ``-d`` are used to bring back the terminal back and run process in background

```docker-compose down``` bring down the service.

