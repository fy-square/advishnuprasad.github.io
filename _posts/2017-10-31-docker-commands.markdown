---
layout: post
title: "Docker commands"
date: 2017-10-31 11:50:54 -0400
comments: true
categories: docker, devops
---

{% codeblock %}
docker pull my_image # Pull my_image from hub.docker.com

docker pull my_image:10.1.2 # Pull a specific version

docker build . # Build image using Dockerfile in current directory

docker images # List docker images

docker build -t my_image . # Build and name the image as my_image

docker run -d -e ENV_NAME=pswd my_image # Run docker image as daemon (-d) and pass env variables (-e) ENV_PASSWORD 

docker ps # List running containers

docker run —-name my-image-1 -d -e ENV_PASSWORD=pswd my_image # Run image in a contianer named my-image-1

docker run —-name my-image-1 -d -e ENV_PASSWORD=pswd -p 3306:306 my_image  # Map and Expose (-p) the docker container on the port 3306 so that it can be accessed from outside the secure docker env

docker-machine ip default  # List ip of default docker 

docker stop my_image # Stop container my_image

docker start my_image # Start continer my_image

docker netwrok ls       # List networks created inside docker

docker network create my-network # Create a custom network (including internal DNS server) so that containers can talk to each other

docker network connect my-network my-image-1 # Add the container (my-image-1) to the network (my-network)

docker run —rm —network my-network my-image-1 # Specify the network while starting the container

docker run --name my-est-my_image -d -v /my/datadir:/var/lib/my_data -e ENV_PASSWORD=pswd  my_image # Map the host volume (-v)  /my/datadir to docker volume /var/lib/my_data

docker tag my_image advp/my_image:1.0 # Tag the image with username and version

docker login  #L ogin to docker repo

docker push advp/my_image:1.0 # Push your image to docker repo

docker ps -a   # List all containers regardless of state

docker rm my-image-1 # Remove comtainer my-image-1

docker rmi my_image # Remove image my_image

docker rmi $(docker images -q -f dangling=true) #Remove unnamed dangling images which may have got created while debugging etc

docker port my-image-1 #List ports exposed by a container

docker top my-image-1 # List processes running in the container

docker exec my-image-1 ls  # Run a command (ls) inside a container (my-image-1)

docker exec -it my-image-1 bash # SSH into the container and access bash

{% endcodeblock %}