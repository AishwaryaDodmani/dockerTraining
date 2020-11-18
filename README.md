# dockerTraining

### **What is Docker?**

- **open platform** for developing, shipping, and running applications.
- enables you to separate your applications from your infrastructure so you can deliver software quickly.
- reduce the delay between writing code and running it in production by making shipping, testing, and deploying code quicker.
- it allows you to run **containers** which are sandboxed process running an application and its dependencies on the host operating system. 
- with Docker, all containers are started based on a Docker **Image**


### **What is a Docker Image?**

- Images are the **blueprint** of docker containers
- An image includes everything needed to run an application - the code or binary, runtimes, dependencies, and any other filesystem objects required.
- We can find existing images at **Docker Image Registry** ie. registry.hub.docker.com/ 

*Command to search a Docker image in the registry:*

    `docker search <name>`

 ***name**: image repository name* 


### **What is a Docker Container?**

- Fundamentally, a container is nothing but a running process, with some added encapsulation features applied to it in order to keep it isolated from the host and from other containers
- with Docker, all containers are started based on a Docker **Image**


*Command to run a container based on the docker image*

     `docker run <options> <image-name>`

***Note**: by default it runs in the foreground, **-d** ie. daemon makes it run in background*

*Example:*

    `docker run -d redis:3.2`

*Command to list all the running containers, their images and the uptime and also a friendly name and id*

     `docker ps`
 
*Command that provides more details about a running container:*

     `docker inspect <friendly-name|container-id>`

*Command to display messages that the container writes to std error and std out*

     `docker logs <friendly-name|container-id>`

*If a service needs to be accessible by a process not running in a container, then the port needs to be exposed via the Host.
ie. ports are bound when containers are started using*

     `-p <host-port>:<container-port> option`
 
*Example:*

     `docker run -d --name redisHostPort -p 6379:6379 redis:latest`
 
***Note**: The container Port ad application port needs to be the same, whereas host port can be anything thats free*

*Command to dynamically allocate a host port
Example:*

     `docker run -d --name redisDynamic -p 6379 redis:latest`

*Command to know which host port has been assigned when its allocated dynamically 
Example:*

    `docker port redisDynamic 6379`


### **DOCKERFILE**

- The Dockerfile is a list of instructions describing how to deploy your application.
- Dockerfiles are the recipes of Docker images
- Docker images are built based on a Dockerfile. 
- A Dockerfile defines all the steps required to create a Docker image with your application configured and ready to be run as a container. 
- The image itself contains everything, from operating system to dependencies and configuration required to run your application.
- Hence it can be run in any environment as everything is contained in the image olly



#### Lets create a Dockerfile!!

Lets take an example of a nginx docker file

```
FROM nginx:alpine   *defines our base image*
COPY . /usr/share/nginx/html  *copies the content of the current directory into a particular location inside the container* 
```

*Command to build the image from the dockerfile in the current dir*

     `docker build -t <image_name:version> <path of the Dockerfile>`
 
Example: 

     `docker build -t webserver-image:v1 .`

*Command to list the images on the host*

    `docker images`


**Add-on:**

- The Dockerfile allows for images to be composable, enabling users to extend existing images instead of building from scratch. 
- By building on an existing image, you only need to define the steps to setup your application. 
- The base images can be basic operating system installations or configured systems which simply need some additional customisations.



### DOCKERFILE CREATION IN DETAIL

To define a base image we use the instruction
`FROM <image-name>:<tag>`
tag is the version

With the base image defined, we need to run various commands to configure our image. 
There are many commands to help with this, the main commands two are COPY and RUN.
`RUN <command>`

**Note**:RUN cmd allows you to execute any command as you would at a command prompt, for example installing different application packages or running a build command. The results of the RUN are persisted to the image so it's important not to leave any unnecessary or temporary files on the disk as these will be included in the image.

`COPY <src> <dest>`
Allows you to copy files from the directory containing the Dockerfile to the container's image. This is extremely useful for source code and assets that you want to be deployed inside your container.
You need to define which port application needs to be accessible on.

Using the `EXPOSE <port>` command you tell Docker which ports should be open and can be bound to.
We now need to define the command that launches the application.

The CMD line in a Dockerfile defines the default command to run when a container is launched.

Example:

`cmd -a "arga value" -b argb-value`

**Note**:An alternative approach to CMD is ENTRYPOINT.


**Dockerfile and the commands for a nodejs app**

```
FROM node:10-alpine

RUN mkdir -p /src/app

WORKDIR /src/app

COPY package.json /src/app/package.json

RUN npm install

COPY . /src/app

EXPOSE 3000

CMD [ "npm", "start" ]
```

*Command to build an image from the Docker file*

        `docker build -t my-nodejs-app . `

*Command to launch the built image is*

        `docker run -d --name my-running-app -p 3000:3000 my-nodejs-app`

**Testing Container**

You can test the container is accessible using curl. If the application responds then you know that everything has correctly started.

`curl http://localhost:3000`

**Note:** Using -e option, you can set the name and value as -e NODE_ENV=production

Example: 
    `docker run -d --name my-production-running-app -e NODE_ENV=production -p 3000:3000 my-nodejs-app`



### Docker Volumes

- Volumes are the preferred mechanism for **persisting data** generated by and used by Docker containers.
- Docker Volumes are created and assigned when containers are started. 
- Docker Volumes allow directories to be shared between containers and container versions.
- Data Volumes allow you to map a host directory to a container for sharing data.
- This mapping is bi-directional.
- Docker Volumes allows you to upgrade containers, restart machines and share data without data loss.

**Add-on**
    In addition, volumes are often a better choice than persisting data in a container’s writable layer, because a volume does not increase the size of the containers using it, and the volume’s contents exist outside the lifecycle of a given container.
    
    
*Command to create data volume while running a container*

   ``` 
   docker run  -v /docker/redis-data:/data \
  --name r1 -d redis \
  redis-server --appendonly yes
  ```
  
*Command to mount the same directory in a different container*

  ```
  docker run  -v /docker/redis-data:/backup ubuntu ls /backup
  ```
  
 *Command to map volumes from the source container to the container being launched*
 
 ```
 docker run --volumes-from r1 -it ubuntu ls /data
 ```
 
 *Command to specify read only access on the volume*
 
 ```
 docker run -v /docker/redis-data:/data:ro -it ubuntu rm -rf /data
 ```
  


# dockerTraining

### **What is Kubernetes?**
An orchestration tool to manage docker containers

### **What is a Minikube**
- Minikube is a tool that makes it easy to run Kubernetes locally. 
- Minikube runs a single-node Kubernetes cluster inside a VM on your laptop for users looking to try out Kubernetes or develop with it day-to-day. 

### ** Kubectl CLI**
- The cluster can be interacted with using the kubectl CLI. 
- This is the main approach used for managing Kubernetes and the applications running on top of the cluster.

Details of the cluster and its health status can be discovered via 
    `kubectl cluster-info`

To view the nodes in the cluster using 
    `kubectl get nodes`

**Note:** If status is Ready, it is ready to accept applications for deployment and the containers can now be deployed.

Command to create and deploy containers onto the cluster
    `kubectl create deployment first-deployment --image=katacoda/docker-http-server`

The status of the deployment can be discovered via the running Pods
    `kubectl get pods`

**Note:** Once the container is running it can be exposed via different networking options, depending on requirements. One possible solution is NodePort, that provides a dynamic port to a container.

    `kubectl expose deployment first-deployment --port=80 --type=NodePort`

**Lets deploy a container onto a cluster**
- The Kubernetes dashboard allows you to view your applications in a UI.

- The run command creates a deployment based on the parameters specified, such as the image or replicas

- This deployment is issued to the Kubernetes master which launches the Pods and containers required. 

- Kubectl run_ is similar to docker run but at a cluster level.

- The format of the command is 
    `kubectl run <name of deployment> <properties>`

- Example:    
    `kubectl run http --image=katacoda/docker-http-server:latest --replicas=1`

- You can then use kubectl to view the status of the deployments
    `kubectl get deployments`

- To find out what Kubernetes created you can describe the deployment process.
    `kubectl describe deployment http`

- The description includes how many replicas are available, labels specified and the events associated with the deployment. These events will highlight any problems and errors that might have occurr

- With the deployment created, we can use kubectl to create a service which exposes the Pods on a particular port.
    `kubectl expose`

- command to expose the container port 80 on the host 8000 binding to the external-ip of the host.
    `kubectl expose deployment http --external-ip="172.17.0.53" --port=8000 --target-port=80`

    `curl http://172.17.0.53:8000` and see the response

- With kubectl run it's possible to create the deployment and expose it as a single command.

- command to create a second http service exposed on port 8001.
    `kubectl run httpexposed --image=katacoda/docker-http-server:latest --replicas=1 --port=80 --hostport=8001`
    
- You should be able to access it using curl http://172.17.0.53:8001

**Note:**
Under the covers, this exposes the Pod via Docker Port Mapping. As a result, you will not see the service listed using 
kubectl get svc

- To find the details you can use 
    `docker ps | grep httpexposed`

- With our deployment running we can now use kubectl to scale the number of replicas.

- Scaling the deployment will request Kubernetes to launch additional Pods. These Pods will then automatically be load balanced using the exposed Service.

- The command kubectl scale allows us to adjust the number of Pods running for a particular deployment or replication controller.
    `kubectl scale --replicas=3 deployment http`

- Once each Pod starts it will be added to the load balancer service. By describing the service you can view the endpoint and the associated Pods which are included.
    `kubectl describe svc http`


**Deployment.yaml**

```apiVersion: apps/v1
kind: Deployment
metadata:
  name: webapp1
spec:
  replicas: 1
  selector:
    matchLabels:
      app: webapp1
  template:
    metadata:
      labels:
        app: webapp1
    spec:
      containers:
      - name: webapp1
        image: katacoda/docker-http-server:latest
        ports:
        - containerPort: 80
  ```

- This is deployed to the cluster with the command 
    `kubectl create -f deployment.yaml`

    `kubectl describe deployment webapp1`


- Kubernetes has powerful networking capabilities that control how applications communicate. These networking configurations can also be controlled via YAML.

- The Service selects all applications with the label webapp1. As multiple replicas, or instances, are deployed, they will be automatically load balanced based on this common label. The Service makes the application available via a NodePort.

**service.yaml**

```
apiVersion: v1
kind: Service
metadata:
  name: webapp1-svc
  labels:
    app: webapp1
spec:
  type: NodePort
  ports:
  - port: 80
    nodePort: 30080
  selector:
    app: webapp1
```

- Deploy the Service with 
    `kubectl create -f service.yaml`

