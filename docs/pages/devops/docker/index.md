# Docker

Docker is a platform for developing, shipping, and running applications in containers. Containers allow a developer to package up an application with all parts it needs, such as libraries and other dependencies, and ship it all out as one package. By doing so, the developer can be sure that the application will run on any other Linux machine regardless of any customized settings that machine might have that could differ from the machine used for writing and testing the code.

## Docker commands

### Create an image from a Dockerfile

```shell
docker build -t {imageName} -f {path}
```

### Run a container

It is used to create and start a container. It is like `docker create {imageId}` + `docker start -a {containerId}`.

> `-a` is used to attach the standard input, output, and error streams to the terminal.

```shell
docker run {imageId}
```

### Executing commands in a running container

```shell
docker exec -it {containerId} {command}
```

- `-i` is used to keep the standard input open i.e. stdin.
- `-t` is used to allocate a pseudo-tty to container's stdout and stderr.
- `{command}` is the command to be executed in the container.

### List all containers

```shell
docker ps --all
```

### List all images

```shell
docker images
```

### Restart a running container

```shell
docker restart {containerId}
```

### Start a stopped container

```shell
docker start {containerId}
```

### Stop a container

```shell
docker stop {containerId} #SIGTERM
```

```shell
docker kill {containerId} #SIGKILL
```

`kill` sends a `SIGKILL` signal to the container, which immediately terminates the container. `stop` sends a `SIGTERM` signal to the container, which allows the container to exit gracefully by waiting for about 10 seconds and if it still does not stop then it sends a `SIGKILL` signal.

### Remove a container

```shell
docker rm {containerId}
```

### Remove all stopped containers

```shell
docker system prune
```

### Remove an image

```shell
docker rmi {imageId}
```

### Remove all containers

```shell
docker container rm $(docker ps -a -q)
```

### Remove all images

```shell
docker image rmi $(docker images -q)
```

### Retrieve logs from a container

```shell
docker logs {containerId}
```