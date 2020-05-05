### Installation

* How to install docker on **Ubuntu**
  https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-16-04

* Install with Homebrew on **Mac OS**
  https://www.code2bits.com/how-to-install-docker-on-macos-using-homebrew/
```
$ brew update                           # Fetch latest version of homebrew and formula.
$ brew tap caskroom/cask                # Tap the Caskroom/Cask repository from Github using HTTPS.
$ brew search docker                    # Searches all known Casks for a partial or exact match.
$ brew cask info docker                 # Displays information about the given Cask
$ brew cask install docker              # Install the given cask.
$ brew cleanup                          # Remove any older versions from the cellar.
```

---

### General

* How to get **shell** on docker container
https://stackoverflow.com/questions/20932357/how-to-enter-in-a-docker-container-already-running-with-a-new-tty#26496854
  ```
  docker exec -it [container-id] bash
  ```

* How to edit (vim) file in docker container
  - Get shell (see above)
  - Install vim (if needed)
  ```
  apt-get update
  apt-get install vim
  ```
  - vim the file
  ```
  vim [FILE]
  ```

* How to avoid image and container buildup
  - Remove all untagged images by combining `docker rmi` with the recent `dangling=true` query:
  ```
  docker images -q --filter "dangling=true" | xargs docker rmi
  ```
  - Docker won't be able to remove images that are behind existing containers, so you may have to remove stopped containers with `docker rm` first:
  ```
  docker rm `docker ps --no-trunc -aq`
  ```

### Steps to get a docker container and run
* Pull from repo
```
$ docker pull elceef/dnstwist
```
* Run the new container
```
$ docker run elceef/dnstwist domain.name
```


### Commands
```
## List Docker CLI commands
docker
docker container --help

## Display Docker version and info
docker --version
docker version
docker info

## Execute Docker image
docker run hello-world

## List Docker images
docker image ls

## List Docker containers (running, all, all in quiet mode)
docker container ls
docker container ls --all
docker container ls -aq

## List running containers
docker ps
docker stop <containerid>
docker rm <containerid>
docker rmi <imageid>

docker build -t friendlyhello .  # Create image using this directory's Dockerfile

docker run -p 4000:80 friendlyhello                   # Run "friendlyname" mapping port 4000 to 80
docker run -d -p 4000:80 friendlyhello                # Same thing, but in detached mode
docker container ls                                   # List all running containers
docker container ls -a                                # List all containers, even those not running
docker container stop <hash>                          # Gracefully stop the specified container
docker container kill <hash>                          # Force shutdown of the specified container
docker container rm <hash>                            # Remove specified container from this machine
docker container rm $(docker container ls -a -q)      # Remove all containers
docker image ls -a                                    # List all images on this machine
docker image rm <image id>                            # Remove specified image from this machine
docker image rm $(docker image ls -a -q)              # Remove all images from this machine
docker login                                          # Log in this CLI session using your Docker credentials
docker tag <image> username/repository:tag            # Tag <image> for upload to registry
docker push username/repository:tag                   # Upload tagged image to registry
docker run username/repository:tag                    # Run image from a registry

docker exec -it <hash> /bin/bash                      # Connect to container execute bash

docker inspect --format '{{ .NetworkSettings.IPAddress }}' $(docker ps -q) # get the ip address of a container


docker stack ls                                            # List stacks or apps
docker stack deploy -c <composefile> <appname>  # Run the specified Compose file
docker service ls                 # List running services associated with an app
docker service ps <service>                  # List tasks associated with an app
docker inspect <task or container>                   # Inspect task or container
docker container ls -q                                      # List container IDs
docker stack rm <appname>                             # Tear down an application
docker swarm leave --force      # Take down a single node swarm from the manager


docker logs <container>         # View logs for container
docker inspect --format='{{.LogPath}}' <container id>   # Lists container log file

```

By default docker uses the json-file driver to record your containers logs and the raw json output of the logs can be found in:
```
/var/lib/docker/containers/[container-id]/[container-id]-json.log
```
You can get this location by running:
```
docker inspect --format='{{.LogPath}}' [container-id or container-name]
```
