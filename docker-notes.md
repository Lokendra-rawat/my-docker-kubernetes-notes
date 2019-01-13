# Docker notes
---

## ---------------------------- Why Docker -------------------------------
- make setup software easy on any machine
- docker cli is used to issue commands to the docker server 
- docker server does the task of creating images,running containers etc

## What is container
- is a set of processes that have a grouping of resources assigned to it 

## Basic commands
- <code> docker run image-name </code> command-to-execute-in-the-container
- <code> docker ps </code>  list all running containers
- <code> docker ps --all </code> list all containers ever created
--------------------
- <code> docker create </code> create container and returns and id 
- <code> docker start -a </code> start container -a is used to get the o/p of the running container
- eg. <code> docker create busybox echo hi there</code> gives us container id
- then <code>  docker start -a containerid </code> paste recieved container id
----------
- <code> docker system prune </code> delete all containers
- <code> docker logs containerId </code> show container logs without starting the container
- <code> docker stop containerId </code>
- <code> docker kill containerId </code>

## Run multiple commands
------------
- <code> sudo docker exec -it 6a6948d0bad0 redis-cli </code> 
-  -it helps to enter in any text input

## Run terminal inside container 
----
- <code> sudo docker exec -it 6a6948d0bad0 sh </code>
- <code> sudo docker run -it busybox sh </code> 

## Create custom docker image
- use an existing image docker image as base
- download and install a dependency 
- Tell the image what to do when it starts as a container 
- then use <code> docker build . </code>  to run the custom image
---
## Tagging an Image
- <code> use docker build -t yourdockerid/imagename:latest . </code>
- specifies the dir to use for the build
---
## Setting base image
- <code> node:alpine </code>
- which alpine version of image with node preinstalled every image has a alpine version of it use : to define the image
---
## Copying files to the image
- <code> COPY ./ ./ </code>
- first (./) is the path to folder to copy from your machine. 
- second (./) is the plach to copy stuff to inside container  
---
## Port Mapping

 map current machine port to the running container port
 
- <code> docker run -p 8080:8080 imageid/imagename </code>
- first one is for routing incoming requests to the port on local host to the port of the speciefied container 
- ---

## Add working directory
- <code> WORKDIR /usr/app </code>
---
## Minimizing cache busting and rebuilds
- since we only need package.json to run npm install so we first copy package.json
- and then run npm install 
---
## --------------------------- Docker Compose ------------------------------

- use to separate cli that gets installed along with docker 
- use to start multiple docker containers at the same time 
- automates some of the long arguments that we passed to the docker run commands
 - docker-compose.yml looks something like this now both the containers can share ports with each other. no port mapping needed all handled by docker-compose
 - and in place of some address in the redis host field we are simply going to add redis-server
 - user <code>docker-compose up</code> to build all the containers add <code>-d</code>to launch in background and <code> --build </code> to rebuild the container
<pre>
  #docker-compose.yml
  version: '3'
  services:
    redis-server:
      image: 'redis'
    node-app:
      build: .
      ports:
        - "3000:8081"
</pre>
<pre>
  #express application
  const express = require('express')
  const redis = require('redis')
  const app = express()
  const client = redis.createClient({
    host: 'redis-server',
    port: 6379
  })
  client.set('visits', 0)
  app.get('/', (req, res) => {
    client.get('visits', (err, visits) => {
      res.send('no of visits is ' + visits)
      client.set('visits', parseInt(visits) + 1)
    })
  })
  app.listen(8081, () => {
    console.log('listening on port 8081')
  })
</pre>
## Stop all running containers
- <code> docker-compose down </code>

## Restart crashed containers 
- restart policies are 
- <code>"no"</code> never attempt to restart this
- <code>always</code> if container stops for a reason always restarts it
- <code>on-failure</code> only starts when the container stops with an error code 
- <code>unless-stopped</code> Always restart unless we(developers) forcibly stop it
- <code> docker-compose ps</code> to show running containers just like docker
---

## Run custom docker files for development 

- <code>docker build -f dockerfile.dev .</code>
- -- 

## Use docker volume 
