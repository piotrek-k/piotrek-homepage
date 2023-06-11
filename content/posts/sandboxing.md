---
title: "Sandboxing using Docker - cheatsheet"
date: 2023-06-11T20:06:59+02:00
draft: false
---

The following cheatsheet is meant to show how to use Docker to sandbox untrusted code. The goal is to minimize the damage that could be made by malicius code downloaded from untrusted source.

Running a code this way allows to:
* prevent code from accessing the rest of the filesystem
* easily revert all changes
* run a code with near native performance
* control network access
* run processes as superuser without giving them controll of entire device

[More on Docker security](https://docs.docker.com/engine/security/)

The main downside of using Docker is that it shares the same kernel as the host, which means that vulnerabilities of host's kernel could be used to escape from container. There are other ways to sandbox code like virtualization, AppArmor, firejail.

## Run a command in a disposible container

```
docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
```

Example for python:
```bash
docker run -it --rm --name my-running-script -v "$PWD":/usr/src/myapp -w /usr/src/myapp python:3 python script.py
```

It crates python3 container, runs a `python script.py` command and removes it as soon as script finishes execution.

Arguments breakdown:
* `-it` runs a command with interactive shell. Allows you to provide input
* `--rm` removes container as soon as it finishes executing
* `-v "$PWD":/usr/src/myapp` points your current directory (on host machine) to path `/usr/src/myapp` inside a container
* `-w` sets working directory to `/usr/src/myapp`
* `python:3` is a base docker image, on which we are running our command
* `python script.py` is a command we want to run inside a container

Effect (only seen when container is still running):
```
$ docker ps -a
CONTAINER ID   IMAGE                                              COMMAND                  CREATED         STATUS                       PORTS     NAMES
3dc148f99f74   python:3                                           "python script.py"       5 seconds ago   Up 4 seconds                           my-running-script
```

For multiple commands (runs `sh` command which then executes chain of commands)
```bash
docker run -it --rm --name my-running-script -v "$PWD":/usr/src/myapp -w /usr/src/myapp python:3 sh -c "python script.py && echo test"
```

## Accessing running container's filesystem

If container is still running (`docker ps -a` returns status "Up"):

```bash
$ docker exec -it b325ffe9fe18 /bin/bash
```

Where `b325ffe9fe18` is a container id.

## Accessing closed container's filesystem

If status of container is "Exited" we cannot enter shell in it. But if we still want to inspect files, processes and make use of stuff in it, we can make snapshot of it and run it independently.

Assuming we create non-disposable container like this:
```bash
docker run -it --name my-running-script -v "$PWD":/usr/src/myapp -w /usr/src/myapp python:3 python script.py
```

And `docker ps -a` command returns:
```bash
$ docker ps -a
CONTAINER ID   IMAGE                                              COMMAND                  CREATED         STATUS                       PORTS     NAMES
69b5fe1b8934   python:3                                           "python script.py"       5 seconds ago   Exited (0) 4 seconds ago               my-running-script
```

Commit the stopped container:
```bash
docker commit 69b5fe1b8934 image_name_of_snapshot
```
Where `image_name_of_snapshot` is a name for newly created image.

Result:
```bash
$ docker images
REPOSITORY                                  TAG                  IMAGE ID       CREATED          SIZE
image_name_of_snapshot                      latest               692b940a0f2a   45 seconds ago   920MB
```

Then to enter shell:
```bash
docker run -it --rm --entrypoint /bin/bash image_name_of_snapshot
```

## More complex scenarios

Create Dockerfile:
```Dockerfile
FROM python:3

WORKDIR /usr/src/app

COPY script.py .

CMD [ "python", "./script.py" ]
```

Then build:
```bash
docker build -t test_base_image . --load
```

Then run and log in to shell:
```bash
docker run -it --entrypoint /bin/bash test_base_image
```

