---
layout: post
title: A Tutorial on Docker
date: 2017-06-29 16:25:00 +0900
categories:
  - Docker
  - Linux
tags:
  - tutorial
  - Ubuntu 16.04
references:
  - https://docs.docker.com/get-started
---

## tl;dr

This post summarizes [Get started with Docker](https://docs.docker.com/get-started) and its commands.

## Installation

Install the community edition of docker:
```shell
sudo apt remove docker docker-engine
sudo apt install apt-transport-https ca-certificates curl software-properties-common

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"

sudo apt update
sudo apt install docker-ce
```

Check the installation:
```shell
docker --version
sudo docker run hello-world
```

Uninstallation:
```shell
sudo apt purge docker-ce
sudo rm -rf /var/lib/docker
```

## Containers

> An image is a lightweight, stand-alone, executable package that includes everything needed to run a piece of software, including the code, a runtime, libraries, environment variables, and config files.
>
> A container is a runtime instance of an image—what the image becomes in memory when actually executed. It runs completely isolated from the host environment by default, only accessing host files and ports if configured to do so.
>
> Containers run apps natively on the host machine’s kernel. They have better performance characteristics than virtual machines that only get virtual access to host resources through a hypervisor. Containers can get native access, each one running in a discrete process, taking no more memory than any other executable.
>
> `Dockerfile` will define what goes on in the environment inside your container. Access to resources like networking interfaces and disk drives is virtualized inside this environment, which is isolated from the rest of your system, so you have to map ports to the outside world, and be specific about what files you want to “copy in” to that environment. However, after doing that, you can expect that the build of your app defined in this `Dockerfile` will behave exactly the same wherever it runs.

See [an example](https://docs.docker.com/get-started/part2/#define-a-container-with-a-dockerfile) for `Dockerfile`.

```shell
# create a docker image, naming and tagging with `-t` <imagename>[:<tag>]
sudo docker build -t <imagename>:<tag>

# see a list of built images
sudo docker images
sudo docker images -a # default hides intermediate images

# run an image with name[:tag]
sudo docker run <imagename>:<tag>

# run with mapping port 4000 to 80 via `-p`
sudo docker run -p 4000:80 <imagename>:<tag>

# run in detached mode using `-d`
sudo docker run -d -p 4000:80 <imagename>:<tag>

# see a list of containers running
sudo docker ps
sudo docker ps -a # includes ones not running

# stop a container with container id <hash>, which is shown in `ps`
sudo docker stop <hash>
# or force to shutdown a container
sudo docker kill <hash>

# remove a container
sudo docker rm <hash>
sudo docker rm $(docker ps -a -q) # all containers

# remove an image
sudo docker rmi <imagename>
sudo docker rmi $(docker images -q) # all images
```

Working with docker hub:
```shell
sudo docker login
# push it to docker hub
sudo docker push <username>/<repository>:<tag>
# run from docker hub
sudo docker run <username>/<repository>:<tag>
```

## Services

> Services are really just containers in production.

Services are containers in production with configuration via `docker-compose.yml`

Install docker-compose:
```shell
sudo -i
curl -L https://github.com/docker/compose/releases/download/1.14.0/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose
exit

# check the installation
docker-compose --version
```

Add your services in `docker-compose.yml`, see [an example](https://docs.docker.com/get-started/part3/#your-first-docker-composeyml-file).

## Swarms

> A swarm is a group of machines that are running Docker and joined into a cluster.
>
> A swarm is made up of multiple nodes, which can be either physical or virtual machines.

[The official tutorial](https://docs.docker.com/get-started/part4) involves virtual machines via virtualbox.

Install docker-machine:
```shell
curl -L https://github.com/docker/machine/releases/download/v0.12.0/docker-machine-`uname -s`-`uname -m` >/tmp/docker-machine && chmod +x /tmp/docker-machine && sudo cp /tmp/docker-machine /usr/local/bin/docker-machine

# check the installation
docker-machine version
```

Make a swarm environment with virtual machines:
```shell
# Create virtual machines via `docker-machine`
docker-machine create --driver virtualbox myvm1
docker-machine create --driver virtualbox myvm2

# see a list of virtual machines
docker-machine ls
# get basic info of a virtual machine
docker-machine env myvm1
# stop and start again
docker-machine stop myvm2
docker-machine start myvm2
docker-machine stop $(docker-machine ls -q) # stop all

## send a command
# docker-machine ssh <hostname> :: will open a terminal session
# docker-machine ssh <hostname> "command" :: will deliver the command

# execute "docker swarm init" to a virtual machine
docker-machine ssh myvm1 "docker swarm init --advertise-addr 192.168.99.100:2377" # myvm1 is a node manager

# if you need a join token
docker-machine ssh myvm1 "docker swarm join-token -q worker"

# add myvm2 to the swarm
docker-machine ssh myvm2 "docker swarm join --token <token> 192.168.99.100:2377"

# see a list of nodes of the swarm
docker-machine ssh myvm1 "docker node ls"
# get detailed information of a node
docker-machine ssh myvm1 "docker node inspect <nodeId>"
```

Remove a swarm and virtual machines:
```shell
# detach a node from the swarm
docker-machine ssh myvm2 "docker swarm leave"
# kill the swarm by the master node
docker-machine ssh myvm1 "docker swarm leave -f"

# remove all virtual machines and their disk images
docker-machine rm $(docker-machine ls -q)
```

## Stacks

> A stack is a group of interrelated services that share dependencies, and can be orchestrated and scaled together. A single stack is capable of defining and coordinating the functionality of an entire application.

A stack is deployed via `docker stack deploy` with a `docker-compose.yml` file. See [an example](https://docs.docker.com/get-started/part5/#add-a-new-service-and-redeploy).

```shell
# copy a file to the swarm manager (to the path ~/)
docker-machine scp docker-compose.yml myvm1:~

# run this compose file by the swarm manager with naming
docker-machine ssh myvm1 "docker stack deploy -c docker-compose.yml <appname>"

# see a list of services of <appname>
docker-machine ssh myvm1 "docker stack services <appname>"
# see a list of running containers
docker-machine ssh myvm1 "docker stack ps <appname>"

# stop and remove <appname>
docker-machine ssh myvm1 "docker stack rm <appname>"
```
