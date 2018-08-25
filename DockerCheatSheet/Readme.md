#Dockerfile


- [x] 1. Docker Machine
- [x] 2. System Clean Up
- [x] 3. Docker Containers
- [x] 4. Docker Images
- [x] 5. Flattening Docker Images
- [x] 6. Authoring Docker Images with Dockerfiles
- [x] 7. Tagging and Pushing Docker Images


## Docker Machine

    $ docker-machine start
    $ docker-machine stop
    $ docker-machine ls


## System Clean Up
Purging All Unused or Dangling Images, Containers, Volumes, and Networks can be achieved by a single command that will clean up any resources (images, containers, volumes, and networks) that are dangling (not associated with a container):

```
$ docker system prune
```

To additionally remove any stopped containers and all unused images (not just dangling images), add the -a flag to the command:

```
$ docker system prune -a
```


---
## Containers

### General use commands

##### List all Docker containers
```
$ docker ps -a
```

##### List running Docker containers
```
$ docker ps
```

##### List stopped Docker containers
```
$ docker ps --filter "status=exited"
```

-or-

```
$ docker ps -f "status=exited"
```

####Stop all Docker containers
```
$ docker stop $(docker ps -a -q)
```

####Remove all Docker containers
```
$ docker rm $(docker ps -a -q)
```

####Remove all exited Docker containers
```
$ docker rm $(docker ps -a -f "status=exited" -q)
```


---
## Docker Images

## General use commands

```
$ docker images -a
```

##### List dangling images
```
$ docker images -f dangling=true
```

##### Remove dangling images
```
$ docker images purge
```

##### Remove all images
```
$ docker rmi $(docker images -a -q)
```

##### Remove images accordingly with a pattern
```
docker images -a | grep "pattern" | awk '{print $3}' | xargs docker rmi
```

##### Pulling an Image
Download a docker image from a *registry* into the *Docker host*

```
$ docker image pull <image-name>
```

example:

```
$ docker image pull memcached
```


### Create an Image
```
$ docker build -t lighttpd:1.0 .
```

##### Image list

```
$ docker image ls lighttpd:1.0
```

$ docker images -a
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
lighttpd            1.0                 416340998a7f        31 seconds ago      338MB


##### Remove installation files
```
$ docker container run lighttpd:1.0 /bin/sh -c "sudo apt-get purge -y --auto-remove wget build-essential"
```

##### List the last container
```
$ docker container ls -lq
```

Output:
7c25b8d0cf3c


##### Remove installation files
Commit changes made on the container to a new image:

```
$ docker container commit 7c lighttpd:1.1
```

Output:
sha256:5e209800ae4b3b5d3198737c20108d5d94104939461124700e61019d79f1732d


##### List the create image
```
$ docker image ls lighttpd:1.1
```

**
REPOSITORY          TAG                 IMAGE ID            CREATED                  SIZE
lighttpd            1.1                 5e209800ae4b        Less than a second ago   340MB
**


### Comments and Notes
The image size increased because we're just masking the files on the image. We've added a new layer to the image instead of removing files and decreasing the image size.



### Flattening Docker Images

    $ docker container export -o lighttpd-1.1.tar $(docker container ls -lq)
    
    $ ls -l
    
    $ docker image import lighttpd-1.1.tar lighttpd:1.1
    
    $ docker image ls --format 'table {{.Repository}}\t{{.Tag}}\{{.ID}}\t{{.Size}}' lighttpd:1.1
    
    REPOSITORY  TAG\IMAGE IDSIZE
    lighttpd1.1\e3871e87461e131MB
    



### Docker image history

    $ docker image history lighttpd:1.0

    IMAGE   CREATED CREATED BY  SIZECOMMENT
    416340998a7fAbout an hour ago   /bin/sh -c tar -xvf lighttpd-1.4.49.tar.gz  4.83MB
    617a5227be02About an hour ago   /bin/sh -c wget -O lighttpd-1.4.49.tar.gz ht   1.05MB
    aceeccf3262eAbout an hour ago   /bin/sh -c apt-get update &&   232MB
    9a5d7185d3a610 days ago /bin/sh -c #(nop)  CMD ["bash"] 0B
    <missing>   10 days ago /bin/sh -c #(nop) ADD file:f21d7c14104d5d9fa   101MB
    


    $ docker image history lighttpd:1.1
    
    IMAGE   CREATED CREATED BY  SIZECOMMENT
    e3871e87461e4 minutes ago   131MB   Imported from -
    


###Building Docker Images with cache


##### Making Use of the Build Cache
```
$ docker image build --build-arg USER=aws -t awscli:1.0 .
```

Specify a default user in the Dockerfile and create a new image. 
```
$ docker image build -t awscli:1.1 .
```

Since the instruction changed is located in the beginning, the cache won't be of much use.
Let's delete these images and consider to change the instruction to a
```
$ docker rmi awscli:1.0 awscli:1.1
```


## Authoring Docker Images with Dockerfiles

##### Image Manifest Digest
The Image manifest digest is only available on images available from a registry. **Local images** don't contain a manifest digest.


    $ docker image pull ubuntu
    
    $ docker image inspect --format '{{.RepoDigests}}' ubuntu



### COPY
**COPY** instruction should be used for adding **local build content**.
**RUN** instruction should be used for adding **remote build content**.


### Make use of CMD and ENTRYPOINT
Refer to DockerCMD-ENTRYPOINT.md


### HEALTHCHECK

    HEALTHCHECK --interval=3s CMD curl --fail -m 2 http://localhost:80 || exit 1
    
    $ docker container run -d --name nginx --cap-add NET_ADMIN -p 80:80 nginx
    
    $ docker exec -it nginx sh -c "sleep10; ip link set lo down; sleep 15; ip link set lo up" &
    
    $ docker system events --since 30m --filter event=health_status
    


---
# 6. Authoring a Nginx Docker Image

### Planning the Image Content
- Prepare
- Acquire
- Build
- Configure
- Serve

    $ docker container ls -l

    $ docker container logs nginx
            


---
# 7. Tagging and Pushing Docker Images

    $ docker image ls nginx
    $ docker image push nginx
    $ docker image tag nginx samueldossantos/nginx
    $ docker login -u samueldossantos
    $ docker image push samueldossantos/nginx
    
---



##Creating the Container

Create a container:

    $ docker container run alpine apk add -- no -cache python cache python
    
    $ docker container ls -l --format 'table {{.ID}}\t{{.Image}}\t{{.Command}}'


### Inspecting the Changes
Delta between image and container's filesystem can be retrieve:

```
$ docker container diff bbd932851d6b | less $ docker 
```

###Committing the Container to an Image
```
$ docker container commit -m "Added Python" bbd932851d6b my -image:1.0
```

The `docker image ls` command shows the image in the local cache:

    $ docker image ls --format \
    > 'table {{.Repository}}\t{{.Tag}}\{{.ID}}\t{{.Size}}'  my -image:1.0




##Image Manifest Digest

    $ docker image pull ubuntu
    $ docker image inspect --format '{{.RepoDigests}}' ubuntu


##Fully Qualified Docker Image Name
**[registry-host[:port]/][namespace/]repository[:tag|@digest]**


