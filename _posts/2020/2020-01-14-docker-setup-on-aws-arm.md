---
title: "docker-setup-on-aws-arm"
slug: "posts/docker-setup-on-aws-arm"
date: "2020-01-14"
template: "post"
category: "blog"
tags: 
  - "cloud"
  - "blog"
  - "arch/arm"
  - "docker"
banner: "/assets/bg/1.jpg"
toc: false
---

## docker-setup-on-aws-arm

`sudo apt install docker.io`명령으로 설치한다.

만약 snap으로 설치했다면 후술할 추가적인 절차를 해야 한다.

(snap install 명령어 확실하지 않음)`sudo snap install docker --classic` 명령으로 aws arm 64bit 머신에서 snap으로 docker를 설치 후 `docker run hello-world`를 실행해 보면 데몬이 작동하지 않는다고 나오고, 데몬을 돌리려 해도 서비스가 없다는 식으로 나온다. 알고보니 snap으로 설치한 경우 추가 작업이 필요한 것이었다.

추가 작업을 따른 후 재부팅 후 `docker run hello-world`를 해보니 잘 되었다.
(다만 중간에 삽질 과정에서 `systemctl enable docker`? 같은 재부팅시 자동으로 서비스를 시작하게 하는 것으로 보이는 명령을 실행한 적이 있어서 확실하진 않음.)

```console
ubuntu@...:~$ docker.help
Docker snap: Docker Linux container runtime.

Due to the confinement issues on snappy, it requires some manual setup to make docker-snap works on your machine.
We'll take you through the steps needed to set up docker snap work for you on ubuntu core and ubuntu classic.

On Ubuntu classic, before installing the docker snap,
please run the following command to add the login user into docker group.
    sudo addgroup --system docker
    sudo adduser $USER docker
    newgrp docker

On Ubuntu Core 16, after installing the docker snap from store,
you need to connect the home interface as it's not auto-connected by default.
    sudo snap connect docker:home :home

Then have fun with docker in snappy.

ubuntu@...:~$ sudo addgroup --system docker
Adding group `docker' (GID 133) ...
Done.
ubuntu@...:~$ sudo adduser $USER docker
Adding user `ubuntu' to group `docker' ...
Adding user ubuntu to group docker
Done.
ubuntu@...:~$ newgrp docker
ubuntu@...:~$ sudo snap connect docker:home :home
ubuntu@...:~$ sudo reboot
(machine reboots...)

ubuntu@ip-172-31-33-250:~$ docker run hello-world
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
256ab8fe8778: Pull complete 
Digest: sha256:d1668a9a1f5b42ed3f46b70b9cb7c88fd8bdc8a2d73509bb0041cf436018fbf5
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (arm64v8)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/

ubuntu@ip-172-31-33-250:~$ 

```

