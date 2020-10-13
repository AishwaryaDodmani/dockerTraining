# dockerTraining

**What is Docker?**

Docker is an open platform for developers and sysadmins to build, ship, and run distributed applications
Docker enables you to separate your applications from your infrastructure so you can deliver software quickly.

Docker allows you to run containers. 

A container is a sandboxed process running an application and its dependencies on the host operating system. 

The application inside the container considers itself to be the only process running on the machine while the machine can run multiple containers independently.

With Docker, all containers are started based on a Docker Image

Docker Image in brief?

Images are the blueprint of docker containers

These images contain everything required to launch the process; the host doesn't require any configuration or dependencies.

We can find existing images at registry.hub.docker.com/ 
or 
by using the command
docker search <name>
 note: <name> here is the image repository name

Command to run a container based on the docker image

docker run <options> <image-name>

Note: by default it runs in the foreground, “-d” makes it run in background
Eg: 
docker run -d redis:3.2

note: d here is a deamon

Command to list all the running containers, their images and the uptime and also a friendly name and id

 docker ps 
The command that provides more details about a running container:

docker inspect <friendly-name|container-id> 

The command to display messages that the container writes to std error and std out

docker logs <friendly-name|container-id> 

If a service needs to be accessible by a process not running in a container, then the port needs to be exposed via the Host.

ie. ports are bound when containers are started using 
-p <host-port>:<container-port> option
 
example:
 docker run -d --name redisHostPort -p 6379:6379 redis:latest

dynamically allocate a host port
eg: docker run -d --name redisDynamic -p 6379 redis:latest

To know which port has been assigned. 
docker port redisDynamic 6379


DOCKERFILE

The Dockerfile is a list of instructions describing how to deploy your application.

Dockerfiles are the recipes of Docker images

Docker images are built based on a Dockerfile. 

A Dockerfile defines all the steps required to create a Docker image with your application configured and ready to be run as a container. 

The image itself contains everything, from operating system to dependencies and configuration required to run your application.

Hence it can be run in any environment as everything is contained in the image olly



Lets create a Dockerfile!!

Lets take an example of a nginx docker file

FROM nginx:alpine   //defines our base image
COPY . /usr/share/nginx/html  //copies the content of the current directory into a particular location inside the container

command to build the image from the dockerfile in the current dir
docker build -t <image_name:version> <path of the Dockerfile>
eg: 
docker build -t webserver-image:v1 .

command to list the images on the host
docker images


DOCKER IMAGES

Docker images are built based on a Dockerfile.

A Dockerfile defines all the steps required to create a Docker image with your application configured and ready to be run as a container. 

The image itself contains everything, from operating system to dependencies and configuration required to run your application.

The Dockerfile allows for images to be composable, enabling users to extend existing images instead of building from scratch. 

By building on an existing image, you only need to define the steps to setup your application. 

The base images can be basic operating system installations or configured systems which simply need some additional customisations.



DOCKERFILE CREATION IN DETAIL

To define a base image we use the instruction 
FROM <image-name>:<tag>
tag is the version

With the base image defined, we need to run various commands to configure our image. 
There are many commands to help with this, the main commands two are COPY and RUN.
RUN <command> 

note: RUN cmd allows you to execute any command as you would at a command prompt, for example installing different application packages or running a build command. The results of the RUN are persisted to the image so it's important not to leave any unnecessary or temporary files on the disk as these will be included in the image.

COPY <src> <dest> 
allows you to copy files from the directory containing the Dockerfile to the container's image. This is extremely useful for source code and assets that you want to be deployed inside your container.
you need to define which port application needs to be accessible on.

Using the EXPOSE <port> command you tell Docker which ports should be open and can be bound to.
we now need to define the command that launches the application.

The CMD line in a Dockerfile defines the default command to run when a container is launched. 
eg:
cmd -a "arga value" -b argb-value

note:An alternative approach to CMD is ENTRYPOINT.


Dockerfile and the commands for a nodejs app

FROM node:10-alpine

RUN mkdir -p /src/app

WORKDIR /src/app

COPY package.json /src/app/package.json

RUN npm install

COPY . /src/app

EXPOSE 3000

CMD [ "npm", "start" ]


To build an image from the Docker file
        docker build -t my-nodejs-app .

The command to launch the built image is 
docker run -d --name my-running-app -p 3000:3000 my-nodejs-app

Testing Container
You can test the container is accessible using curl. If the application responds then you know that everything has correctly started.

curl http://localhost:3000

Note:Using -e option, you can set the name and value as -e NODE_ENV=production
eg: docker run -d --name my-production-running-app -e NODE_ENV=production -p 3000:3000 my-nodejs-app

