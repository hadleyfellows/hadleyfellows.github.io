# Docker
> Run applications by containers
- [Installation](#installation)
- [.dockerignore](#.dockerignore)
- [Docker Commandline](#docker-commandline)
- [Container Concepts](#container-concepts)
- [Docker Registry](#docker-registry)
- [Dockerfile](#dockerfile)
## Installation
## .dockerignore
## Docker Commandline
## Container Concepts
## Docker Registry
## Dockerfile

DOCKER README

Install
windows - https://github.com/docker/toolbox/releases/tag/v1.12.5
		- https://www.docker.com/products/docker-toolbox#/resources
linux   - https://docs.docker.com/docker-for-windows/install/#download-docker-for-windows

REPO GUIDE
https://hub.docker.com/explore/


CONCEPTS
	Containers vs. VMs
		- VMS are full machines on a physical machine: app,os,hard disk
		- containers share a linux kernel os,hard disk
		"Kernel Namespaces"	- containers have  their own :
				- pid (isolated process tree pid tree)
				- net (network dedicated IP and IP ranges)
				- mnt (mounted folders)
				- user (container root users)
				- cgroups (Control Groups)
					- one cgroup to a contianer
					- this allows us to limit bandwidth, cpu, disk space
				- capabilities for security

	DOCKER containers
		- docker used to called dotCloud
		- Solomon Hykes - main player in creating docker
		- written in GoLang 
		- licenced by apache?
		- libcontainer replaces linux LXC
			- libcontainer allows direct access to Linux Kernal
			- libcontainer allows for cross-platform

	DOCKER INSTALL
		Install docker on ubuntu
			https://docs.docker.com
			- login as root
			- service docker.io status
			- apt-get update
			- apt-get istall -y docker.io 
			- service docker.io status
			- docker -v 
				docker client command asked docker daemon for the version
			- docker version
			- docker info
				containers :
				images :
				...
		Install docker on CentOS
			- sudo su
			- yum install docker
			- systemctl status docker.service
			- systemctl start docker.service
			- docker -v
			- docker version
			- docker info
	Updating Docker
			- backup everything
			- wget -qO- https://get.docker.com/gpg | apt-key add -
			- echo deb http://get.docker.com/ubuntu docker main > /etc/apt/sources.list.d/docker.list
			- apt-get install -y lxc-docker
			- docker version
	BASIC CONFIG DOCKER CONTAINER
			- needs root access
				cat /etc/group
				sudo gpasswd -a <user> docker
				logout thn logon
				docker run -it ubuntu /bin/bash
			- run docker service on network
				docker -H <IP Address> -d &
				netstat -tlp
				test on centos machine
					export DOCKER_HOST="tcp://<ip address>"
					docker version			
	REGISTRIES
			DockerHub
				- registered images or repos 
				- the github of docker
				registry.hub.docker.com

	DOCKER IMAGES
		Layer images
			- called images as well
			- each layer has unqiue ID
			ex.
				base ubuntu image
				second layer nginx
				top layer update layer
				-----
				all these make one image
			union mounts
				- shared file system 
				- with multiple views
				- only top layer is edittable
			bootfs
				- a layer image that only exists during boot of docker
			commands
				docker-machine ip
				docker images 
				docker images --tree
				docker info

				docker pull <image alias>  (pulls images from docker hub to store locally)
				docker pull -a <image alias> 

				docker ps   (list of images currently running)
				docker ps -a (all images ran in the past)
				docker start <id or tag> (start the docker image)
				docker attach <id or tag> (attach to a specific container)
				docker top (shows top processes on image)
				docker inspect <image id or tag> (shows hardware details)

				docker run ubuntu /bin/bash -c "echo 'cool content' > /tmp/cool-file"
				docker run 
					image tag or id : 
						ubuntu (latest ubuntu)
						ubuntu:14.04 (specific ubuntu version)
					executable to run inside: /bin/bash 
					command on boot: -c "echo 'cool content' > /tmp/cool-file"
					run in background: -d
					interactive allows us to log into the image: -it
					control number of cpu shares:  --cpu-shares=256
					control memory allocation: memory=1g^C
					name the container --name=example1

				docker run -it ubuntu /bin/bash  (running docker in the foreground)
					takes you into terminal on the ubuntu container
					press CTRL+P+Q (detaches from the container)
					docker stop <image id> (stops the docker image)
					docker ps -l  (lists last run docker images)
					docker rm <image id> (remmoves containers image, but we cannot remve a running container)
					docker start <image id> (start the docker image)
					docker attach <image id> (attach into docker image)
					docker restart <image id> (restarts docker image)
					docker top <image id> (shows the docker image processes from outside the container)
						docker attach <image id>
						ps -ef (shows processes from inside the docker container)
					docker logs <image id> 
						-f to stream the logs
					docker inspect <image id> (a bunch of information on the image )
						read from config.json and hsotConfig.json on our docker image
						ie.
						/var/lib/containers/<image id>/config.jon ..
				ENTER THE DOCKER CONTAINER
					nsenter
						- namespace enter
						- requires container pid (found from docker inspect <image id> | grep pid)
						ex.
							nsenter -m -u -n -p -i -t <pid> /bin/bash
								- mount namespace
								- uts namespace
								- network namespace
								- process namespace
								- ipc namespace
								- target namespace
								- process id
					docker-enter <image id> (enters the docker image)
					docker exec -it <image id> /bin/bash 

				
				image managment 
					docker commit <image id> <tag> (creaes a new dcoker image)
					docker history <image id or tag>
					docker save -o /tmp/<id or tag>.tar <id or tag> (saves image)
					docker load -i /tmp/<id or tag>.tar (loads docker image)
					docker rmi <image id> (remmoves image, but we cannot remove a running container)



			Files located:
				/var/lib/docker/aufs/
					/var/lib/docker/aufs/layers
						- files with th image id as file name
						- files contain ids of layer images they are built on
						- the layered images are from high level to base level
					/var/lib/docker/aufs/diff
						- shows the top layer image's file structure
						ex. 
							bin
							boot
							home
							lib
							tmp
							usr
							var
							...
					/var/lib/docker/containers/
						list all container images on the system

			pid 1 
				/bin/bash is the pid=1 
				in linux pid=1 the init process
				the init process is like the main class process that manages the other processes
				though docker is made for one process per container we can run multiple processes or daemons


		Dockerfile
			- set of instructions onhow to build an image
			- when building a docker image from Dockerfile all files and directories will be included into the build
			- always start with FROM an image
			- MAINTAINER email address
			RUN
				- every RUN creates a new layer in our docker image
				- RUN is a build time command
			CMD
				- CMD is a run-time command
				- CMD only one command per Dockerfile
				- CMD executes everytime the docker image starts
				- can run shell form or array form ["echo","hello world"]
			ENTRYPOINT
				- can use --entrypoint
				- can't be overwritten at run time
				- takes parameters from command line or CMD command and interret them at runtime
				ex. 
					FROM ubuntu:15.04 
					MAINTAINER hadleysjobs@gmail.com
					ADD example.tar.gz / 
					RUN apt-get update
					#RUN apt-get install -y nginx
					#RUN apt-get install -y golang
					CMD echo $var1
					ENTRYPOINT ["echo"]
				# This will echo any paramters passed in at
				$ docker build -t="hw2" . 
				$ docker run hw2 hellooooo there!
				$ helloooooo there!
				$ docker run -it hw2 /bin/bash
				$ /bin/bash
					FROM ubuntu:15.04
					RUN apt-get update && apt-get install -y iputils-ping
					ENTRYPOINT ["apache2ctl"]
				$ docker build -t="web2" .
				$ docker run -d -p 80:80 web2 -D FOREGROUND

			ENV
			ENV    - ENV var1=hado var2=fellows
				ex. 
					FROM ubuntu:15.04 
					RUN apt-get update && apt-get install -y iputils-ping apache2
					ENV var1=ping var2=8.8.8.8
					CMD $var1 $var2
					ENTRYPOINT ["apache2ctl"]
				$ docker build -t="hado1" .
				$ docker run -it hado1 /bin/bash
				will ping 8.8.8.8 non stop
				docker logs -f hado1
			Volumes
				Allow for shared data between contaiers
				$ docker run -it -v /test-vol --name=voltainer ubuntu:15.04 /bin/bash
				$ docker inspect voltainer
				$ docker run -it --volumes-from=voltainer ubuntu:15.04 /bin/bash
				mount host folder to container folder
				$ docker run -v /host_data_folder:/container_datafolder
				Inside the docker file VOLUME /data will allow the container to use a /data folder on the host
				Deleting volumes
					$ docker rm -v <container>




			Dockerfile commands
				FROM   - FROM ubuntu:15.04
				ADD    - ADD example.tar.gz /
				RUN    - RUN apt-get install -y apache2
				EXPOSE - EXPOSE 80
				CMD    - CMD ["apache2ctl", "-D", "FOREGROUND"]
					   - CMD "echo", "Ello World"
				ENTRYPOINT - ENTRYPOINT ["apache2ctl"]
				ENV    - ENV var1=hado var2=fellows
				VOLUME - VOLUME /data
				WORKDIR - location of files on docker machine


			docker build -f node.dockerfile -t tageName .
			docker history <image layer id>
			docker info
			docker images-tree

			Ex. Dockerfile
				#Ubuntu based Hello World container
				FROM ubuntu:15.04 
				MAINTAINER hadleysjobs@gmail.com
				ADD example.tar.gz / 
				RUN apt-get update
				#RUN apt-get install -y nginx
				#RUN apt-get install -y golang
				CMD "echo","Hello World"


				docker build -t helloworld:0.1 .
					- the dot is the current directory
					- -t is for tags

			Ex. Dockerfile 2
				#web server example
				FROM ubuntu:15.04 
				MAINTAINER hadleysjobs@gmail.com
				# option 1
					RUN apt-get update
					RUN apt-get install -y apache2
					RUN apt-get install -y apache2-utils
					RUN apt-get install -y vim
					RUN apt-get clean
				
				# option 2
				RUN apt-get update \
					&& apt-get install -y \
					apache2 \
					apache2-utils \
					vim \
					&& apt-get clean
					&& rm -rf /var/lib/apt/lists* /tmp/* /var/tmp/*

				EXPOSE 80
				CMD ["apache2ctrl","-D","FOREGROUND"]

				docker build --no-cache -t="webserver-small" .
				docker histry webserver-small
				docker run -d -p 80:80 webserver
				docker stop webserver


			BuildCache
				docker build -t="build1"
				docker build -t="build2" (this happens faster because of build cache)
				whats happing is that it is matching the layer images that it already has in its cache

				docker build --no-cache -t="build with no cache"




		Registries (repo for docker images)
			- Docker hub is a public registry /repo
			- a registry / repo holds docker images
			- push image to docker registry/repo
				docker tag <iamge id> <repo name>
				docker push <repo name>
				<repo name> = fellows/example1
				docker tag 2ui4ui5ij6667u fellows/example1
				docker push fellows/example1

				login and presto
			- pull images from docker registry
				docker pull <repo name>
				<repo name> = fellows/example1
				docker tag 2ui4ui5ij6667u fellows/example1
				docker pull fellows/example1
			- private registry
				*** NOTE ***
				* Ubuntu config
					/etc/default/docker
					DOCKER_OPTS="--insecure-registry host:5000"
				* CentOS config
				ExecStart=/usr/bin/docker -d $OPTIONS $DOCKER_STORAGE_OPTIONS --insecure-registry host:5000
				
				- initialize registry
					docker run -d -p 5000:5000 registry				
				
				- push to private registry
					docker tag <image id> host:5000/priv-test
					docker push host:5000/priv-test

				- pull from private registry
					docker run -d hot:5000/priv-test

		docker networks
			1. look at the networking stack
				ps a
				returns network config
					1: lo: <LOOPBACK,UP,LOWR_UP>
					2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP>
					3: eth1: <BROADCAST,MULTICAST,UP,LOWER_UP>
					4: docker0: <NO-CARRIER,BROADCAST,MULTICAST,UP>
			2. docker0
				a host network bridge or ethernet switch
				apt-get install bridge-utils
				yum install bridge-utils

				$ brctl show docker0
				As you start containers you will see an interface per container
				$ docker attach container 1
				$ ip a
					1: lo: <LOOPBACK,UP,LOWR_UP>
					2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP>
				$ ping 8.8.8.8
				$ traceroute 8.8.8.8
					1 172.17.42.1 (172.17.42.1) 0.04 ms 0.013 ms 0.011 ms
					2 ...
				This is our gateway to the internet 172.17.42.1

				vethx <<<<=====================>>>> eth0
				(docker host namespace)				(Container namespace)

				$ docker inspect
					shows NetworkSettings {}

				network files for Containers
					/var/lib/docker/containers/<container id>
					hosts
					resolv.conf

				every resolv.conf is a copy of the docker host's resolv.conf

				$ docker run --dns=8.8.4.4 --name=dnstest net-img
				$docker run -d -p 5001:80
					- map port 5001 in our docker machine/host to port 80 on our docker container

				$ docker port <container name>
					by default tcp but you can use udp
				$docker run -d -p 5001:80/udp --name=web2 apache-img
				$docker run -d -p <ip>.5001:80/udp --name=web3 apache-img
				$docker run -d -p <ip>.5001:80/udp --name=web3 apache-img
				$docker run -d -P --name=web3 apache-img
					- -P exposes all ports that are marked as exposed in the container files

				LINKING CONTAINERS
					OPTION 1
						source container
							port 80 is exposed
						doker run --name=src -d img
						doker run --name=receiver --link=src:<alias> -it ubuntu /bin/bash
							- inside the reciever image
							- env | grep <alias>
							- we can use src container using the env variables set by --link
					OPTION 2
						docker network create --drive bridge isolated_network
						docker run -d --net=isolated_network --name mongodbNAME mongo
						docker run -d --net=isolated_network --name nodeapp -p 3000:3000

				TROUBLESHOOTING

				PEMZN-SFGWT-CZYG9-L4QTJ-FMAME

					DOCKER SERVICE LOGGING
						docker -d -l debug &
						docker -d -l debug (error, debug, fatal, info) | error (error and fatal) | info (error, fatal, info) | fatal 
						
						vim /etc/default/docker
						DOCKER_OPTS="--log-level=fatal"
						restart docker service and booo ya
					DOCKER CONTAINER LOGGING
						docker logs
					DOCKER IMAGES
						test docker builds by creating a base container of say ubuntu and then run all the Dockerfile commands in the image
					DOCKER NETWORK
						ip link del docker0
						vim /etc/default/docker
						DOCKER_OPTS=--bip=<ip>/24
						ip a
					FIREWALL ON DOCKER HOST
						--icc=true (default)
							inter-container communication
							iptables -L -v
							vim /etc/default/docker
								DOCKER_OPTS=--icc=false
						--iptables= 
							determines whether docker will make mods to iptables
							vim /etc/default/docker
								DOCKER_OPTS=--icc=true --iptables=false




					DOCKER COMPOSE
								docker-compose build (burns the dvd image) --no-cache (choose to not cache)
								docker-compose up (starts up container)
								docker exec -it <container id> /bin/bash (access the image and execute commands)

								reads from docker-compose.yml

								docker-compose build
								docker-compose up
								docker-compose down
								docker-compose build node
								docker run -it dids_node /bin/bash
								docker-compose logs




Images build containers

cd to docker file
	docker-compose build (burns the dvd image) --no-cache (choose to not cache)
	docker-compose up (starts up container)
	docker exec -it <container id> /bin/bash (access the image and execute commands)
docker image

docker images (lists all docker images in docker cache *** need to run docker build to register docker images)
docker run -it <docker image id> /bin/bash



reads from docker-compose.yml

docker-compose build
docker-compose up
docker-compose build node
docker run -it dids_node /bin/bash


docker network (learn more)
docker machine (learn more)
docker swarm   (learn more)
docker registry (Learn more) - like git bare repo

cd /c/home/dev/docker/dids; docker-compose build --no-cache node;


DOCKER DEEP DIVE


DOCKER copy
	docker cp [OPTIONS] CONTAINER:SRC_PATH DEST_PATH|-
	docker cp [OPTIONS] SRC_PATH|- CONTAINER:DEST_PATH

docker cp src/. mycontainer:/target
docker cp mycontainer:/src/. target

WINDOWS CONTAINERS 
	APACHE
		https://www.assistanz.com/installing-apache-web-server-in-windows-container-using-docker-file/
	
  
  
  
  
  
  NEW DOCKER README


REPO GUIDE & HELPFUL LINKS
	https://hub.docker.com/explore/
	Install
		windows - https://github.com/docker/toolbox/releases/tag/v1.12.5
				- https://www.docker.com/products/docker-toolbox#/resources
		linux   - https://docs.docker.com/docker-for-windows/install/#download-docker-for-windows

DOCKER INSTALL
	Install docker on windows
		virtual box
		docker-machine rm default
		docker-machine create --driver virtualbox default
		docker-machine ip
	Install docker on ubuntu
		https://docs.docker.com
		- login as root
		- service docker.io status
		- apt-get update
		- apt-get istall -y docker.io 
		- service docker.io status
		- docker -v 
			docker client command asked docker daemon for the version
		- docker version
		- docker info
			containers :
			images :
			...
	Install docker on CentOS
		- sudo su
		- yum install docker
		- systemctl status docker.service
		- systemctl start docker.service
			- systemctl daemon-reload
			- systemctl restart docker
		- docker -v
		- docker version
		- docker info
	Updating Docker
		- backup everything
		- wget -qO- https://get.docker.com/gpg | apt-key add -
		- echo deb http://get.docker.com/ubuntu docker main > /etc/apt/sources.list.d/docker.list
		- apt-get install -y lxc-docker
		- docker version



DOCKER COMPOSE
	docker-compose build --no-cache (choose to not cache)
		- burns the dvd image
		- reads from docker-compose.yml
	docker-compose up 
		- starts up container
	docker exec -it <container id> /bin/bash 
		- access the image and execute commands


	docker-compose build
	docker-compose up
	docker-compose down
	docker-compose build node
	docker run -it dids_node /bin/bash
	docker-compose logs

DOCKER IMAGES
	
	docker images
	docker images --all
	docker rm <image id>
	docker run -it <docker image id> /bin/bash


DOCKER CONTAINERS
	
	docker ps
	docker ps --all
				docker attach <container id>
				ps -ef (shows processes from inside the docker container)
	docker-enter <container id> (enters the docker image)
	docker exec -it <container id> /bin/bash 
	docker rm <container id> (remmoves containers image, but we cannot remve a running container)
	docker start <container id> (start the docker image)


	docker ps -a
		To list all running and stopped containers
	docker ps -a -f status=running
		To list all running containers (just stating the obvious and also example use of -f filtering option)
	docker ps -aq
		To list all running and stopped containers, showing only their container id
	docker rm `docker ps -aq -f status=exited`
		To remove all containers that are NOT running

	


DOCKER REGISTRIES
	Docker hub is a public registry /repo - repo holds docker images
	
	docker login
	username:
	password:
	docker tag <image id> <server>/<username>/<repo>:<tag>
		- docker tag 68a0826acdbc hado650/test:liquorapp
		- docker push hado650/test:liquorapp
		- docker pull hado650/test:liquorapp


	Private Registry
			*** NOTE on the pushing machine ***
				* Ubuntu config
					/etc/default/docker
					DOCKER_OPTS="--insecure-registry host:5000"
				* CentOS config
					ExecStart=/usr/bin/docker -d $OPTIONS $DOCKER_STORAGE_OPTIONS --insecure-registry host:5000
				* WINDOWS DOCKER VIRTUALBOX
					SSH into your local docker VM. default is the name of the virtualbox
						$ docker-machine ssh default					
					ADD TO EXISTING OR END OF FILE
						$ sudo vi /var/lib/boot2docker/profile											
							EXTRA_ARGS="
							--insecure-registry myserver.pathTo.registry1:5000
							--insecure-registry myserver.pathTo.registry2:5000
							--insecure-registry myserver.pathTo.registry3:5000
							"
					Save the profile changes and 'exit' out of the docker-machine bash back to your machine. Then Restart Docker VM substituting in your docker-machine name
						$ docker-machine restart {machineName}
						My Setup
							docker-machine version : 0.6.0, build e27fb87
							docker-machine driver : virtualbox
			
			- initialize registry
				docker run -d -p 5000:5000 registry				
			
			- push to private registry
				docker tag <image id> host:5000/priv-test:1.1
				docker push host:5000/priv-test:1.1

			- pull from private registry
				docker pull localhost:5000/priv-test:1.1
				docker run -d hot:5000/priv-test

	TAG IT AND BAG IT
		docker tag ed94e1e75ab7 141.167.70.243:5000/fellowsh/tileserver:1.1	
		docker push 141.167.70.243:5000/fellowsh/tileserver:1.1
	RUN CONTAINER IMAGE TO RESTART ALWAYS
	docker run --name jenkins_master -d -p 8080:8080 -p 50000:50000 -v jenkins_home:/var/jenkins_home jenkins/jenkins:lts --restart=always



DOCKER TROUBLESHOOT
	- build fails out of memory
		docker rm $(docker ps -q -f 'status=exited')
		docker rmi $(docker images -q -f "dangling=true")






DOCKER WINDOWS

Steps to resolve the trouble...

Thanks to Julien Corioland
https://blogs.msdn.microsoft.com/jcorioland/2016/10/13/getting-started-with-windows-containers/
provides steps
article "Cloud Computing, Architecture, Containers and DevOps !"

On windows 10 in elevated PowerShell
Enable-WindowsOptionalFeature -Online -FeatureName containers -All
Check
Get-WindowsOptionalFeature -Online -FeatureName "Microsoft-Hyper-V"
and add if needed "Microsoft-Hyper-V"
In docker task bar options switch to using Windows containers.
Using Windows Task Manager end task for docker service -> start service
Thanks to author of next issue above for the detailed log and the comment by friism. Apparently in the default installation docker is trying to start Linux VM with 2GB of RAM and apparently my hardware configuration wasn't satisfactory to this requirement. I am not certain this will resolve all outstanding issues, but finally getting prompt "Docker is running" was a good starting point.





Install-PackageProvider ContainerImage -Force
Find-ContainerImage
Enable-WindowsOptionalFeature -Online -FeatureName Containers
Install-ContainerImage WindowsServerCore

CTRL PQ

https://github.com/OneGet/MicrosoftDockerProvider

https://blog.docker.com/2016/09/build-your-first-docker-windows-server-container/




https://docs.microsoft.com/en-us/virtualization/windowscontainers/manage-docker/configure-docker-daemon
https://docs.microsoft.com/en-us/virtualization/windowscontainers/quick-start/quick-start-images


try first:
http://blogs.recneps.net/category/Windows-Containers


Install-windowsFeature containers
Restart-Computer
https://aka.ms/tp5/b/dockerd
https://aka.ms/tp5/b/docker
copy to programfiles\docker
add to system path
dockerd --register-service
Start-Service docker
Get-Service docker
docker version
Install-PackageProvider ContainerImage -Force
Find-ContainerImage
Install-ContainerImage WindowsServerCore
Restart-Service docker
docker images
docker run -it windowsservercore cmd

https://docs.microsoft.com/en-us/virtualization/windowscontainers/manage-docker/configure-docker-daemon


DOCKER CENTOS

Install DOcker CE on Centos 7

https://docs.docker.com/install/linux/docker-ce/centos/

$ yum install --setopt=obsoletes=0 \
   docker-ce-17.03.2.ce-1.el7.centos.x86_64 \
   docker-ce-selinux-17.03.2.ce-1.el7.centos.noarch # on a new system with yum repo defined, forcing older version and ignoring obsoletes introduced by 17.06.0


docker-compose
sudo curl -L https://github.com/docker/compose/releases/download/1.21.0/docker-compose-$(uname -s)-$(uname -m) -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
docker-compose --version

systemctl enable docker.service


  
