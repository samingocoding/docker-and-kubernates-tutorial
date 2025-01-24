# docker-and-kubernates-tutorial
Docker And-kubernates-tutorial

Docker is an open-source platform that allows developers to automate the deployment, scaling, and management of applications. It uses containerization technology to package an application and its dependencies into a standardized unit for software development. Docker containers ensure that an application works seamlessly in any environment, eliminating the "it works on my machine" problem. This makes Docker incredibly useful for developing and distributing applications in a consistent and predictable way.

To practice create account on docker hub

Go to [https://labs.play-with-docker.com/](https://labs.play-with-docker.com/p/cnf3m52im2rg00f3ojjg)
to start running play code for docker

## Docker commands

Here is a brief explanation of some commonly used Docker commands:

- `docker run`: This command is used to start a new container from an image.
- `docker ps`: This command lists all the running containers.
- `docker stop`: This command stops a running container.
- `docker rm`: This command removes a container.
- `docker images`: This command lists all the images stored locally.
- `docker rmi`: This command removes an image.
- `docker pull`: This command pulls an image or a repository from a registry.
- `docker build`: This command builds an image from a Dockerfile.
- `docker login`: This command is used to log in to a Docker registry.
- `docker push`: This command pushes an image or a repository to a registry.
- `docker commit`: This command creates a new image from a container's changes.

Please note that this is not an exhaustive list, and Docker provides many other commands for more specific tasks.

## Docker Now

Docker is now widely used due to its many advantages. It provides a consistent environment for applications from development to production, eliminating the common issue of discrepancies between these environments. This ensures that the application works predictably anywhere. Docker also isolates applications in containers, making it easy to manage and secure applications independently of each other. Furthermore, Docker is lightweight compared to traditional virtual machines, as containers share the host system's kernel, and it starts up faster. Additionally, Docker has a vast community and ecosystem providing ready-to-use images for various applications, which can significantly speed up development and deployment time.

# Installation on Linus server

<aside>
💡 curl -fsSL https://get.docker.com -o get-docker.sh

</aside>

<aside>
💡 sh get-docker.sh

</aside>

# Running a docker container

If container dose not exist on your local machine use 

<aside>
💡 docker container run --publish 80:80 nginx:latest

</aside>

If container exists use start command

<aside>
💡 docker container start name_of_container

</aside>

# What happens behind when running  a container in Docker

- Docker looks for the specified image locally.
- If the image is not found locally, Docker pulls it from the Docker Hub.
- Docker creates a new container from that image.
- Docker allocates a filesystem to the container, where it can run and isolate its processes and network activity from the host machine.
- Docker runs the command specified in the Dockerfile to start the application within the container.

# Docker Container Commands

## Docker Container Commands

- `docker container stats`: This command displays a live stream of container(s) resource usage statistics. It provides an overview of CPU, memory, network I/O, block I/O, and PIDs for all running containers or a specific container if the container's name is specified.
- `docker container inspect`: This command shows detailed information about a container in JSON format. It includes the container's configuration, state, network settings, and other information.
- `docker container logs`: This command fetches the logs of a container. This is useful for debugging a running or a stopped container.
- `docker container pause`: This command pauses all processes within a container. It's useful when you want to temporarily suspend a container's operations without stopping it.
- `docker container unpause`: This command unpauses a paused container, allowing it to continue its processes.
- `docker container restart`: This command restarts a running container. It's useful when you've made changes to a Dockerfile or other configuration that the container depends on.
- `docker container exec`: This command allows you to run a command in a running container. It's often used for debugging or when you need to interact with a running container.
- `docker container attach`: This command attaches your terminal's input, output, and error (or any combination of the three) to a running container. It's useful when you want to interact with a running container.
- `docker container kill`: This command sends a SIGKILL to a running container, forcing it to stop immediately. Use this command with caution, as it does not provide the container the opportunity to exit gracefully.

Remember, this is not an exhaustive list, and Docker provides many other specific commands for managing containers.

### Shell inside a container

Use the 

<aside>
💡 docker container run -it {image} command

</aside>

To start new container interactively

<aside>
💡 docker container exec -it

</aside>

to run additional command in existing container

# Docker Caching

Docker caching is a mechanism that speeds up the image building process by reusing layers from previous builds. When Docker builds an image, it executes each instruction in the Dockerfile and creates a new layer. If an instruction hasn't changed since the last build, Docker can reuse the cached layer instead of creating a new one.

## Best Practices for Docker Caching

- **Order Dependencies Wisely:** Put instructions that change less frequently at the beginning of your Dockerfile, and instructions that change more frequently towards the end.
- **Combine Related Commands:** Use multi-line RUN instructions with && to reduce the number of layers while keeping the commands logically grouped.
- **Use .dockerignore:** Exclude unnecessary files from the build context to prevent cache invalidation.
- **Copy Dependencies First:** Copy dependency files (like package.json) before copying the rest of the application code.

Here's an example of a Dockerfile optimized for caching:

```
# Bad Example - Poor Caching
FROM node:14
WORKDIR /app
COPY . .
RUN npm install
CMD ["npm", "start"]

# Good Example - Optimized for Caching
FROM node:14
WORKDIR /app
# Copy only package files first
COPY package*.json ./
# Install dependencies
RUN npm install
# Then copy the rest of the code
COPY . .
CMD ["npm", "start"]
```

In the optimized example, if your application code changes but package.json remains the same, Docker can use the cached npm install layer, significantly reducing build time.

### Advanced Caching Techniques

- **Multi-stage Builds:** Use multi-stage builds to separate build dependencies from runtime dependencies, resulting in smaller final images.
- **BuildKit:** Enable BuildKit for advanced caching features by setting DOCKER_BUILDKIT=1 environment variable.
- **Cache Mounts:** Use --mount=type=cache to share cache between builds for operations like package management.

Example of a multi-stage build with advanced caching:

```
# Build stage
FROM node:14 AS builder
WORKDIR /app
COPY package*.json ./
RUN --mount=type=cache,target=/root/.npm \
    npm install
COPY . .
RUN npm run build

# Production stage
FROM nginx:alpine
COPY --from=builder /app/dist /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

# Docker Networks

Docker Networks provide the communication interface between the Docker containers. They are the way that Docker containers talk with each other. Docker networking allows the containers running on the same Docker host to communicate with each other using the IP addresses. Docker does not support automatic service discovery on the default bridge network but you can establish links between containers to provide the necessary connectivity.

There are three common types of Docker networks:

- **Bridge**: This is the default network type. If you don’t provide a network option to docker run command then the docker container will be attached to the bridge network.
- **Host**: If you use the host network mode for a container, that container’s network stack is not isolated from the Docker host, and use the host’s networking directly.
- **Overlay**: Overlay networks connect multiple Docker daemons together and enable swarm services to communicate with each other. You can also use overlay networks to facilitate communication between a swarm service and a standalone container, or between two standalone containers on different Docker daemons.

You can create your own network and specify it when starting a Docker container. This can be done using the 'docker network create' command and specifying the network on 'docker run' with the '--network' option.

### Commands

Here are some commonly used Docker network commands:

- `docker network ls`: This command lists all the networks created on the Docker host.
- `docker network create`: This command allows you to create your own network.
- `docker network rm`: This command allows you to remove a network.
- `docker network connect`: This command connects a container to a network.

> docker network connect <id for network> <id of container>
> 
- `docker network disconnect`: This command disconnects a container from a network.
- `docker network inspect`: This command displays detailed information about a specific network.

> docker network inspect internal
docker container run --network internal --name internal-app nginx
docker network create internal
> 

<aside>
💡

</aside>

# Docker Image

A Docker image is a lightweight, stand-alone, executable package that includes everything needed to run a piece of software, including the code, a runtime, libraries, environment variables, and config files. These images are the basis of containers. When a Docker container is run, it runs an instance of the image.

Features of a Docker Image:

- **Layered**: Docker images are made up of layers, and each layer corresponds to a Dockerfile instruction. The layers are stacked and each one is a delta of the changes from the previous layer.
- **Immutable**: Once a Docker image is created, it does not change. This ensures that the same image can be reliably used across different stages of the software development lifecycle.
- **Version Controlled**: Docker images support versioning, and different versions of the same image can exist simultaneously.
- **Portable**: Docker images can be shared and used across different environments.
- **Lightweight**: Docker images are smaller in size compared to virtual machine images as they share the host system's kernel.

Components of a Docker Image:

- **Base Image**: This is the image that is built upon to create a new Docker image. It can be an OS image like ubuntu, or even another Docker image.
- **Dockerfile**: This is the script with instructions to build a Docker image.
- **Layers**: These are the changes that are applied to the base image to form the new image. Each instruction in the Dockerfile creates a new layer in the image.
- **Image ID**: This is a unique identifier for each Docker image.
- **Tags**: These are the readable names that point to the image ID. They are used to easily identify and pull images.

### Docker image commands

Here are some commonly used Docker image commands:

- `docker image ls`: This command lists all the Docker images that are currently stored in your local machine.
- `docker image rm`: This command removes one or more Docker images from your local machine.
- `docker image pull`: This command pulls a Docker image from a registry (like Docker Hub) to your local machine. If no tag is specified, Docker pulls the `latest` tag.
- `docker image push`: This command pushes a Docker image from your local machine to a registry.
- `docker image build`: This command builds a Docker image from a Dockerfile and a "context". The build's context is the set of files located in the specified PATH or URL.
- `docker image inspect`: This command displays detailed information on one or more images in a JSON array. This can be useful to get specific details like the image's ID, when it was created, the size of the image, and more.
- `docker image tag`: This command assigns a tag to an image, so it has a readable name.
- `docker image save`: This command saves one or more images to a tar archive.
- `docker image load`: This command loads an image from a tar archive as a Docker image.
- `docker image prune`: This command removes all unused images.
- `docker image history` : This commands shows all the previouse changes ( layers ) made to a docker image

## Docker Tagging and Push To Docker hub

Docker tagging is the process of assigning a tag, which is a readable alias, to a Docker image. This tag is used to identify and pull the image from a Docker registry. By default, Docker assigns the 'latest' tag to an image if no tag is specified. However, it's best practice to assign meaningful tags to your Docker images, especially for version control.

To tag a Docker image, you use the `docker tag` command:

<aside>
💡 docker tag source_image:tag target_image:tag

</aside>

After tagging your Docker image, you can push it to a Docker registry such as Docker Hub. Pushing your image makes it available for others to pull and use. To push an image to Docker Hub, first, ensure you are logged in to Docker Hub in your command line using `docker login`. Then, use the `docker push` command:

<aside>
💡 docker push target_image:tag

</aside>

Updating tags is also a common practice, especially when you have made changes to the image and want to update the version. To update a tag, you simply issue the `docker tag` command again with the new tag.

Remember, when you update a tag and push the image, the new image will replace the older version in the Docker registry. However, the older version will still be accessible using its unique image ID.

# Docker File

A Dockerfile is a text file that contains all the commands a user could call on the command line to assemble an image. It supports a set of commands that you can use and some best practices on how to use them effectively. Here are some commonly used instructions in a Dockerfile:

- `FROM`: This instruction initializes a new build stage and sets the base image for subsequent instructions.
- `LABEL`: It adds metadata to an image.
- `RUN`: This command allows you to execute any commands in a new layer on top of the current image and commit the results.
- `CMD`: This provides defaults for an executing container. “Execued when a container is run not image”
- `COPY`: This copies new files or directories from <src> and adds them to the filesystem of the container at the path <dest>.
- `ADD`: This instruction copies new files, directories or remote file URLs and adds them to the filesystem of the image at the path <dest>.
- `EXPOSE`: This informs Docker that the container listens on the specified network ports at runtime.
- `WORKDIR`: This sets the working directory for any RUN, CMD, ENTRYPOINT, COPY and ADD instructions that follow in the Dockerfile.
- `ARG`: This defines a variable that users can pass at build-time to the builder with the docker build command.
- `ENV`: This sets the environment variable <key> to the value <value>. This value will be in the environment for all subsequent instructions in the build stage.

Here is an example of a Dockerfile for an Angular application deployed using Nginx:

```
# Stage 1: Compile and Build angular codebase

# Use official node image as the base image
FROM node:latest as build

# Set the working directory
WORKDIR /app

# Add the source code to app
COPY ./ /app/

# Install all the dependencies
RUN npm install

# Generate the build of the application
RUN npm run build

# Stage 2: Serve app with nginx server

# Use official nginx image as the base image
FROM nginx:alpine

# Copy the build output to replace the default nginx contents.
COPY --from=build /app/dist /usr/share/nginx/html

# Expose port 80
EXPOSE 80

# Run nginx
CMD ["nginx", "-g", "daemon off;"]

```

In this Dockerfile, we use a multi-stage build process. In the first stage, we are using Node to build our Angular app. Then we copy the build output from stage 1 into the Nginx server in stage 2. This way, we get a clean, production-ready Docker image that doesn't include any unnecessary build tools or dependencies.

# Running Docker file

When a Dockerfile is run using the `docker build` command, Docker reads the Dockerfile, executes the instructions in the order they are written, and returns a Docker image. Here is a more detailed breakdown of the process:

1. Docker starts with the base image specified by the `FROM` instruction.
2. Docker then executes the remaining Dockerfile instructions one by one, in the order they are written.
3. For each instruction, Docker creates a new layer in the image if there is a change from the previous layer, and commits the results. This is why Docker images are composed of multiple layers.
4. After all the instructions have been executed, Docker finalizes the image and generates a unique image ID and assigns the tag specified in the Dockerfile.

One important aspect of running a Dockerfile is the build cache. If you build a Docker image and all the instructions in the Dockerfile are identical to a previous build, Docker will use the cache from the previous build for the layers. This significantly speeds up the build process.

However, if an instruction in the Dockerfile changes compared to a previous build, Docker will execute that instruction and all subsequent instructions, resulting in new layers that are not from the cache. This is because a change in one layer could influence all the layers that come after it.

For example, if you have a Dockerfile with 5 instructions, and you change the 3rd instruction, Docker will use the cache for the first 2 instructions, then execute the 3rd instruction and the remaining instructions without using the cache. This ensures that the Docker image accurately represents your Dockerfile.

You can also bypass the cache and force Docker to execute all instructions in a Dockerfile by using the `--no-cache=true` option with the `docker build` command. This might be necessary in certain situations where you want to ensure a completely fresh build, but it will make the build process slower.

Remember, Docker's caching mechanism is a powerful feature that can significantly speed up your image builds, but it's important to understand how it works so you can use it effectively.

### Additional notes

1. You can run a container in detached mode 

```jsx
docker run -d <image>
```

 2. start a container in attached mode 

```jsx
docker container attach <container name >.      / -a
```

1. You can start a container in interactive mode as well attached 

```jsx
docker container -a-i <container name>
```

1. Get logs from a running container

```jsx
docker logs -f <container name> 
```

1. Removing Containers / images 

```jsx
docker container -rm <container name >
docker -rmi imageid, imageid
```

1. Running image and deleting it at the same time 

```jsx
docker run -p 3306:80 -rm imageid
```

1. You can copy files in and out of a container 

```jsx
docker cp imagename:/folder/file destination
docker cp blissful_borg:/sam.txt test/
```

1. You can tag a docker image 

```jsx
docker build -t node:mytag .
#Running you can use the tag as well creating a container
docker run name:tag 
```

1. Share docker image on Docker hub

```jsx
docker login
#rename docker image on pc with tag
docker tag image:current dockerhubname:latest
docker push dockerhubname:latest
```

## Docker Ignore

A `.dockerignore` file is a text file that tells Docker to ignore certain files and directories during the build process of the Docker image. This file works much like a `.gitignore` file. It should be in the root directory of the context.

The syntax of the `.dockerignore` file is similar to the `.gitignore` file. You specify a pattern on each line, and any file or directory that matches this pattern will be ignored by Docker. If a directory matches the pattern, all files in that directory are also ignored.

Here are a few examples of patterns you can use in a `.dockerignore` file:

- `.log`: Ignore all log files.
- `/tmp`: Ignore the directory named tmp in the root of the context.
- `temp?`: Ignore all files/directories whose name starts with `temp` and is followed by exactly one character.
- `*/temp*`: Ignore files beginning with `temp` in any directory recursively.

Using a `.dockerignore` file can help you reduce the size of the Docker build context, which in turn can speed up the build process. It can also help you avoid accidentally including sensitive data in your Docker images.

Here is an example of a `.dockerignore` file:

```
# Ignore all log files
*.log

# Ignore all .json files
*.json

# Ignore node_modules directory
node_modules/

# Ignore private key
*.pem

```

To use a `.dockerignore` file, simply create the file in the root directory of your Docker context and add the patterns for the files and/or directories you want Docker to ignore. The next time you run `docker build`, Docker will ignore the specified files and directories.

## Alpine in Docker

Alpine Linux is a lightweight, security-oriented, and general-purpose Linux distribution based on musl libc and busybox. In the context of Docker, Alpine images are known for their much smaller sizes compared to other distribution based images.

Using Alpine, you're able to make your Docker images and containers as small as possible. This can lead to significant savings in resources, time, and disk space, especially when you're deploying or scaling a large number of containers.

In order to use Alpine in Docker, you specify it as your base image in your Dockerfile using the `FROM` instruction, just like any other base image. For example:

```
FROM alpine:latest

```

The above line in a Dockerfile tells Docker to base the new image on the latest version of Alpine Linux.

Some benefits of using Alpine in Docker include:

- **Efficiency**: Due to its small size, Alpine images download faster and use less disk space. This can speed up build times, deployment times, and start times for your Docker containers.
- **Security**: Alpine uses musl libc, which is designed with security in mind, and all binaries are compiled as Position Independent Executables (PIE) with stack-smashing protection.
- **Simplicity**: Alpine uses busybox, an assortment of Unix utilities combined into a single executable, providing a streamlined container environment.
- **Compatibility**: Despite its small size, Alpine is a full-featured Linux distribution. This means you can install packages and run applications just like you would in other Linux distributions.

<aside>
💡 Use Alpine tags for every image to use
nginx:alpine

</aside>

# Docker Tags and Versions

You have full control of an image by using tags

## Docker Tags and Versioning

Docker tags serve as a versioning mechanism for Docker images. Each tag represents a different version of the image. By default, if you do not specify a tag, Docker will use the "latest" tag. However, it's a best practice to use more informative tags for version control.

To apply a tag to a Docker image, you use the `docker tag` command:

```
docker tag source_image:tag target_image:tag

```

Here, `source_image` is the ID or name of the image you want to tag, and `target_image:tag` is the name and tag you want to apply.

Once an image is tagged, you can push it to a Docker registry like Docker Hub using the `docker push` command:

```
docker push target_image:tag

```

This command uploads your tagged image to the registry, making it available for others to pull and use.

You can also retag an image. If you have updated an image and want to apply a new tag to it, simply use the `docker tag` command again with the new tag. When you push this newly tagged image, it will replace the existing image with the same tag in the Docker registry.

Remember, Docker tags are mutable, meaning they can be reassigned to new images. This is useful for maintaining a "latest" version, but it can lead to confusion if not managed carefully. It's a good practice to use immutable, version-specific tags for production images to ensure consistency and traceability.

## Docker Inspect

"Docker Inspect" is a powerful command used in Docker to fetch low-level information about Docker objects such as containers, images, volumes, networks, and services. This command provides detailed information about the specified object in JSON format.

For example, if you want to inspect a container, you can use the command `docker inspect container_id_or_name`. This will output various details about the container, including its ID, creation time, path, arguments, state (status, running, paused, restarting, etc.), network settings, and much more.

`docker inspect` can be particularly useful for troubleshooting or when you need to know specific details about your Docker objects that are not provided by other Docker commands. It's an essential tool for anyone working with Docker on a regular basis.

## Docker Logs

"Docker Logs" is an essential command used in Docker to fetch the logs of a container. This command is particularly useful when you want to troubleshoot your Docker containers or applications running inside your containers.

You can use the command `docker logs container_id_or_name` to output the logs of a specific container. By default, this will display the entire log output from the container since it was started.

Docker provides several options to customize the log output:

- `f`, `-follow`: This option allows you to follow the log output in real-time, much like the `tail -f` command in Unix systems.
- `-since`: This option allows you to show logs since a certain time. You can use a timestamp or a relative time such as "1h" (one hour) or "10m" (ten minutes).
- `-until`: This option allows you to show logs until a certain time, using the same formats as `-since`.
- `t`, `-timestamps`: This option adds a timestamp to each log line.
- `-tail`: This option allows you to limit the output to the last N lines, similar to the `tail` command in Unix systems.

Remember that while `docker logs` is a powerful tool for troubleshooting, it's also a good practice to have a centralized logging system in place when you're running multiple containers or deploying your containers in production environments.

## Docker Volumes

Docker volumes are a way to persist data generated by and used by Docker containers. They allow data to be stored separately from the container itself, making it easier to manage and share data between containers or with the host system.

Here's how you can create and name a Docker volume:

1. **Creating a Volume**: You can create a volume using the **`docker volume create`** command followed by the name you want to give to the volume.
    
    ```bash
    docker volume create my_volume
    ```
    
    This command creates a Docker volume named **`my_volume`**.
    
2. **Naming a Volume during Container Creation**: Alternatively, you can also specify the name of the volume when you create a container using the **`-mount`** flag with the **`source`** option.
    
    ```bash
    docker run -d --name my_container --mount source=my_volume,target=/path/in/container my_image
    ```
    
    In this example, a Docker container named **`my_container`** is created, and it's attached to the volume named **`my_volume`**. The **`target`** parameter specifies the path inside the container where the volume will be mounted.
    
    ```jsx
    docker container run -d -e MYSQL_ALLOW_EMPTY_PASSWORD=True --name mysql-volume --mount source=my_volume,target=/var/lib/mysql mysql
    
    /**Another way **/
    docker container run -d -e MYSQL_ALLOW_EMPTY_PASSWORD=True --name mysql-volume -v my_volume:/var/lib/mysql mysql
    
    ```
    
3. **Using Named Volumes in Docker Compose**: If you're using Docker Compose, you can define named volumes in your **`docker-compose.yml`** file like this:
    
    ```yaml
    yamlCopy code
    version: '3'
    services:
      my_service:
        image: my_image
        volumes:
          - my_volume:/path/in/container
    
    volumes:
      my_volume:
    
    ```
    
    In this example, a volume named **`my_volume`** is defined in the **`volumes`** section of the **`docker-compose.yml`** file. It's then mounted to the container **`my_service`** at **`/path/in/container`**.
    

Naming volumes can help you manage and reference them more easily, especially when dealing with multiple containers or when sharing data between containers.

### Bind Mounting

Bind mounting in Docker is a method of mounting a file or directory from the host machine into a Docker container. Unlike volumes, which are managed by Docker and stored within Docker's storage space, bind mounts point directly to a path on the host filesystem.

It Provides the following 

1. **Usage**: Bind mounts are useful when you need to provide access to specific files or directories on the host machine to the containers. This can include configuration files, logs, or any other data that needs to be accessed or modified by both the host and the container.
2. **Syntax**: To use bind mounting, you specify the path on the host machine followed by a colon and then the path inside the container where you want to mount the volume.
    
    ```jsx
    docker run -v /host/path:/container/path my_image
    
    /*****/
     docker container run -d -p 8080:80 --name bind-nginx -v $(pwd):/usr/share/nginx/html nginx
    ```
    
3. **Mounting Files or Directories**: You can bind mount both individual files and entire directories. When you bind mount a directory, all the files and subdirectories inside it will be accessible from within the container.
4. **Real-Time Synchronization**: Changes made to files or directories through bind mounts are immediately reflected both on the host machine and inside the container. This allows for real-time synchronization of data between the host and the container.
5. **Permissions and Security**: Bind mounts inherit the permissions and ownership of the files and directories on the host machine. This means that any user or process inside the container will have the same level of access to the bind-mounted files as they do on the host.
6. **Limitations**: Bind mounts are dependent on the directory structure and file paths of the host machine. If the path specified in the bind mount command does not exist on the host, Docker will create it. However, if there are conflicts or permissions issues, it can lead to errors or unexpected behavior.

## Docker Compose

Docker Compose is a tool that allows you to define and manage multi-container Docker applications. It uses YAML files to configure the application's services and performs the creation and start-up process of all the containers with a single command.

With Docker Compose, you can manage your application's services in a very systematic way, which can be especially helpful in a development environment when you need to manage multiple services.

Some of the main features of Docker Compose include:

- **Single host deployment**: Docker Compose uses a single host for deployment, which makes it a great tool for development environments, automated testing, and staging environments.
- **Quick and easy configuration**: Due to its easy syntax, Docker Compose simplifies the configuration process. You can specify your application’s environment and service configuration in a single file, then create and start all services from this configuration.
- **High productivity**: Docker Compose reduces the time it takes to perform tasks such as building, pulling, and deploying containers, which can significantly increase productivity.

Here is an example of how to use Docker Compose:

First, you create a 'docker-compose.yml' file in your project directory, specifying the services that make up your app:

```yaml
version: '3'
services:
  web:
    build: .
    ports:
     - "5000:5000"
  redis:
    image: "redis:alpine"

```

In this example, there are two services: web and redis. The web service is built using the Dockerfile in the current directory, and it forwards the exposed port 5000 on the container to port 5000 on the host machine. The redis service uses a public Redis image pulled from the Docker Hub registry.

Then you use the `docker-compose up` command to start your entire application. Docker Compose will start and run your entire app with just this command.

Use the `docker-compose down` to stop all containers and remove unnecessary  volumes 

[Reverse Proxy](https://www.notion.so/Reverse-Proxy-231489e1d69042ad9edcfae1888993d3?pvs=21)

[**Dockerizing Angular Project**](https://www.notion.so/Dockerizing-Angular-Project-1bd2a683eeda4ba1ae7201b1902b6a13?pvs=21)

<aside>
💡

This docker network mostly used when  you want to run applications or containers in one network

</aside>

## Docker Swarm

Docker Swarm is Docker's native clustering and orchestration solution that turns a group of Docker hosts into a single, virtual Docker host. It allows you to manage multiple Docker nodes as a cluster, making it easier to deploy and scale applications across multiple hosts.

Key features of Docker Swarm include:

- **Cluster Management:** Automatically handles the clustering of Docker hosts and maintains their state.
- **Decentralized Design:** Docker Swarm follows a decentralized design where services can be deployed across multiple nodes, ensuring high availability.
- **Scaling:** Easily scale services up or down by adjusting the number of replicas for each service.
- **Load Balancing:** Built-in load balancing distributes service containers across the cluster.

Basic Docker Swarm commands:

```bash
# Initialize a swarm
docker swarm init

# Join a swarm as a worker
docker swarm join --token <worker-token> <manager-ip>:2377

# List nodes in the swarm
docker node ls

# Deploy a service
docker service create --name my-service --replicas 3 nginx

# Scale a service
docker service scale my-service=5

#Shows the specific service details
docker service ps <service name>

#Updating repicas of a service 
docker service update <service ids> --replicas 3 

#In Swarm if you remove a container it will be automaticaly add by the servie 
docker container rm -f <container-name>

#Remove service by 
docker service rm <service-name>
```

Docker Swarm operates in two main modes:

- **Manager Nodes:** Handle cluster management tasks and orchestrate container deployment. It's recommended to have an odd number of managers for fault tolerance.
- **Worker Nodes:** Execute containers as assigned by manager nodes. They can be added or removed dynamically as the workload changes.

When using Docker Swarm, services are the primary means of running applications. A service defines how a particular image should be deployed and managed across the cluster, including aspects like:

- Number of replicas
- Network and storage configurations
- Port mappings
- Update policies

<aside>
Note: While Docker Swarm is built into Docker Engine and is easier to set up, Kubernetes has become more popular for container orchestration in production environments due to its broader feature set and larger ecosystem.

</aside>

## Docker Volume

Docker volumes are the preferred way to persist data generated and used by Docker containers. They are essential for maintaining data persistence across container lifecycles.

### Types of Data Stored in Docker Volumes

- **Database Files:** Storing database data files, ensuring data persistence even if the container is removed
- **Configuration Files:** Maintaining application configurations that need to persist between container restarts
- **Log Files:** Storing application logs for monitoring and debugging purposes
- **User-Generated Content:** Storing uploads, documents, and other user-created files
- **Shared Application Data:** Data that needs to be shared between multiple containers

### Volume Types in Docker

1. **Named Volumes:** Managed by Docker and stored in a part of the host filesystem that's managed by Docker
2. **Anonymous Volumes:** Similar to named volumes but with randomly generated names, typically used for temporary storage
3. **Host Volumes (Bind Mounts):** Direct mapping to a specific path on the host machine

### Volume Management Commands

```bash
# List volumes
docker volume ls

# Create a new volume
docker volume create my_volume

# Inspect a volume
docker volume inspect my_volume

# Remove unused volumes
docker volume prune

# Remove a specific volume
docker volume rm my_volume
```

Volumes provide several advantages over writing data directly to a container's writable layer:

- **Better Performance:** Volumes are more efficient for writing and reading data compared to container's storage
- **Data Persistence:** Data in volumes persists even after the container is deleted
- **Data Sharing:** Volumes can be safely shared and reused among multiple containers
- **Independent Updates:** Volume content can be modified directly without affecting the container image

### Additional Notes

Problem : If you delete a container the data tat was created inside would be gone also.
Solution: Docker volumes ⇒ folders in host machine mapped inside a container.

By Reading and writing both ways

There 2 types of external data storage in Docker 

1. Volumes ⇒ Managed by Docker via docker commands . It creates a path on host machine cant be find
    - Named Volumes ⇒ given a name when running and one cant find their path. not attached to container data will remain
    - Anonymous ⇒ auto created by Docker without names not closely attached to a container data can be lost
2. Bind Mounts ⇒ hosts binds data to a container where you define a path and reflected in the container . No need to reload or rebuild an image and it persist or editable code

```jsx
docker run -d --rm -p 3000:80 --name feedback-node -v feeback:/app/feedback feedback:volues
# If you run this its a named volume data stored on host even when container is removed you can still access the files
```