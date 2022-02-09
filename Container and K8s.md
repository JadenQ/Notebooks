### Container

#### Build docker application

##### Create requirements.txt (for packaging python libraries’ dependencies)

```
$ vim requirements.txt
# requirements.txt
Flask==2.0.2
```

##### Create Dockerfile

```
$ vim Dockerfile
FROM python:3.10.1
LABEL maintainer="tds019@ie.cuhk.edu.hk"
COPY . /app
WORKDIR /app
RUN pip install -r requirements.txt
EXPOSE 4330
CMD python main.py
```

##### Build the image

```
$ ls
Dockerfile       main.py          requirements.txt templates
$ docker build -t myflask:1.0 .
$ docker images
REPOSITORY              TAG           IMAGE ID       CREATED             SIZE
myflask                 1.0           1d7ab0712b96   22 minutes ago      928MB
...
```

##### Run a container from the myflask image

```
$ docker run -d --name simple_web -p 5730:4330 myflask:1.0
$ docker ps
CONTAINER ID   IMAGE         COMMAND                  CREATED         STATUS         PORTS                              NAMES
4cf4b61a77ac   myflask:1.0   "/bin/sh -c 'python …"   9 minutes ago   Up 9 minutes   8080/tcp, 0.0.0.0:5730->4330/tcp   simple_web

$ docker stop simple_web
simple_web

$ docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES

$ docker ps -a
CONTAINER ID   IMAGE         COMMAND                  CREATED          STATUS                       PORTS     NAMES
4cf4b61a77ac   myflask:1.0   "/bin/sh -c 'python …"   10 minutes ago   Exited (137) 7 seconds ago             simple_web

$ docker rm simple_web
simple_web

$ docker ps -a
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```

##### Delete the image

```
docker rmi <your-image-id>
```

#### Useful Docker Commands

##### Image

- `docker build -t myimage:1.0 .` build an image from the Dockerfile in the current directory and tag the image.
- `docker image ls` or `docker images` list all images that are locally stored with the Docker Engine.
- `docker image rm alpine:3.4` or `docker rmi alpine:3.4` delete an image from the local image store.
- `docker rmi $(docker images -q)` delete all images

##### Container

- `docker container run --name web -p 5000:80 alpine:3.9` or simply `docker run --name web -p 5000:80 alpine:3.9`run a container from the Alpine version 3.9 image, name the running container “web” and expose port 5000 externally, mapped to port 80 inside the container.

- `docker container run --name web -p 5000:80 -i alpine:3.9` run the above container in interactive mode.

- `docker container run -d --name web -p 5000:80 alpine:3.9` run the above container in the background.

- ```
  docker container stop web
  ```

  or

  ```
  docker stop <id>
  ```

  Stop a running container through SIGTERM.

  - `docker stop $(docker ps -a -q)` stop all containers

- `docker container kill web` Stop a running container through SIGKILL.

- `docker network ls` List the networks.

- `docker container ls` or `docker ps` list the running containers (add `--all` or `-a` to include stopped containers).

- `docker container rm -f $(docker ps -aq)` or `docker rm $(docker ps -a -q)` delete all running and stopped containers.

- `docker container logs --tail 100 web` or `docker logs --tail 100 web` print the last 100 lines of a container’s logs.

- `docker exec -it 665b4a1e17b6 bash` create an interactive bash shell to running instance by ID

##### Docker Management

- `docker app*` Docker Application
- `docker assemble*` Framework-aware builds (Docker Enterprise)
- `docker builder` Manage builds
- `docker cluster` Manage Docker clusters (Docker Enterprise)
- `docker config` Manage Docker configs
- `docker context` Manage contexts
- `docker engine` Manage the docker Engine
- `docker image` Manage images
- `docker network` Manage networks
- `docker node` Manage Swarm nodes
- `docker plugin` Manage plugins
- `docker registry*` Manage Docker registries
- `docker secret` Manage Docker secrets
- `docker service` Manage services
- `docker stack` Manage Docker stacks
- `docker swarm` Manage swarm
- `docker system` Manage Docker
- `docker template*` Quickly scaffold services (Docker Enterprise)
- `docker trust` Manage trust on Docker images
- `docker volume` Manage volumes

##### Share

- `docker pull myimage:1.0` pull an image from a registry.
- `docker tag myimage:1.0 myrepo/myimage:2.0` retag a local image with a new image name and tag.
- `docker push myrepo/myimage:2.0` push an image to a registry.

##### DockerFile

- Check: https://docs.docker.com/engine/reference/builder/
- (Exercise) Understand the usage of `FROM`, `RUN`, `CMD`, `LABEL`, `EXPOSE`, `ENV`, `ADD`, `COPY`, `ENTRYPOINT`, `VOLUME`, `USER`, `WORKDIR`, etc.
- Understand the differences of the following:
  - `ADD` vs. `COPY`
  - `RUN` vs. `CMD` vs. `ENTRYPOINT`

##### Docker Compose

- Using a YAML file to configure your application’s services
- Check: https://docs.docker.com/compose/
- `docker-compose build` build docker-compose.yml file
- `docker-compose up` start docker-compose.yml file

##### Docker-Hub

- Check: https://docs.docker.com/docker-hub/
- Similar to GitHub.

##### Reference

https://docs.docker.com/ 

https://www.ibm.com/cloud/blog/containers-vs-vms

https://docs.docker.com/engine/reference/builder/

https://yeasy.gitbook.io/docker_practice/

https://joshhu.gitbooks.io/dockercommands/content/

### K8s

