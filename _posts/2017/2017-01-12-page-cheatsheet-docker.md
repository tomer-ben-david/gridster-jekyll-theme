---
layout: post
title:  "Docker cheasheet"
date:   2017-01-12 22:18:00
categories: cheatsheet,containers,devops
comments: true
---
**ubuntu bin/bash docker**

`docker run -it ubuntu /bin/bash`

**docker run**

argument to `docker run` such as `/bin/bash` overrides and `CMD` command we wrote in `Dockerfile`

`ENTRYPOINT` cannot be overriden at run time with normal commands `docker run <command>` what we do specify at the end of `docker run <command>` is provided as arguments to the `ENTRYPOINT` in this way a container is as a binary as `ls`.

so `CMD` can act as default parameters to `ENTRYPINT` and then we can override the `CMD` args from `<command>`.

`ENTRYPOINT` can be overriden with `--entrypoint`

**ENV**

```bash
ENV key=value key2=value
CMD $key $key2
```

**docker info**

`docker info`: shows summary of how many containers we have how many images.

```bash
$ docker info
Containers: 74
 Running: 0
 Paused: 0
 Stopped: 74
Images: 31
```

**docker insepct**

shows lot of info about the container you can view them directly at:

`ls -l /var/lib/docker/containers/3487jhf.../` `config.json` shows the `inspect` value.

on mac there is a vm image: `ls -lh ~/Library/Containers/com.docker.docker/Data/com.docker.driver.amd64-linux/`

**docker attach**

attaches to `PID1` of the container so if PID1 is not ssh, here is what we do:

`docker exec -it 87sdfhsjh /bin/bash`

**docker top**

shows all process inside the docker container but it shows the `PID` from outside the container.

**show last container which was run**

`docker ps -l`

**Show docker containers including exited**

`docker ps -a`

**Show how a docker image was created (the commnands)** 

`docker history imagename`

**docker stop**

it sends a `SIGTERM` to the container `PID1` and the container terminates when PID1 terminates
 
**sending other sigterms**

`docker kill -s <SIGNAL>` which goes to `PID1`

**what does docker `run` command does**

reads the images and `stacks` them one on another and adds a new writable top layer.

**check if docker installed only on ubuntu**

`service docker.io status`

`docker.io` because on linux there was already some old service `docker` (so to install `sudo apt-get install docker`)

**docker mount volume**

when deleting a container without `-v` the volumes are not deleted so delete with

`docker rm -v <container>`

**security `docker group`**




`sudo gpasswd -a yourusername docker`

instead of using `root` to communicate (run docker commands) with docker socket you can add users to the `docker unix group`

`docker run -v /some/local/dir:dirindocker redis`

**docker daemon to listen on network and not local socket file**

```bash
docker -H 192.168.56.50:2375 -d &
export DOCKER_HOST="tcp://192.168.56.50:2375
# client connects to docker daemon
docker info
```

**what happens to data when you stop a docker container**

if you edited a file inside the consitner and you stop and start your container file still exists, it exists on the actual disk under the host instance id so when you start its still there unless you remove the conatiner with `docker rm`.  This data is not in the `docker image` unless you `commit` it, it was just set on the currently running docker instance.

**take volume mounted on r1 container and mount to ubuntu**

`docker run --volumes-from r1 -it ubuntu ls /data`

(motivation: no need to mount same volume twice code duplication)

**mount `/docker/redis-data` as read only volume**

`docker run -v /docker/redis-data:/data:ro -it ubuntu rm -rf /data`

**logs**

```bash
docker logs redis-server
docker logs -f redis-server # like tail -f
```

note your possibilities with logs are to `stdout/stderr` or you can redirect to `syslog` this can be useful if you already have systems which take syslog and manage it.

**auto restart**

`docker run -d --name restart-3 --restart=on-failure:3 scrapbook/docker-restart-example`

auto restart on `exitcode !== 0` up to 3 times.

`restart-always`: will restart it indefinitely. 

**images**

multiple containers using the same lower level containers won't need to transfer those images if already exist they share those images.

**Image layering**

If an upper image has the same file as lower level image then the higher level image wins.  This using the `union mounts` feature.
The top layer is the only writable layer.  When the container boots there is another layer! below the OS layer `bootfs` layer which gets gone after the container has booted.


**show images layering**



`docker images --tree`

on ubuntu you would see all local image layers directly at

`ls -l /var/lib/docker/aufs/layers`

if you `cat` the top layer it would just print to console the `uuid`s of the below layers.

to look at the `difference` which a layer adds type (on `ubuntu`)

`ls -l /var/lib/docker/aufs/diff/87384728.../` and you would see the file diff of this layers from below!

export docker image

`docker save -o /tmp/imagename.tar imagename`

examine the tar

`tar -tf /tmp/imagename.tar`

`docker load -i /tmp/imagename.tar`


**upload image to repo**

first tag

```bash
docker tag 2347823hfj tomerbregistry/helloworldrepo:1.0
docker push tomerbregistry/helloworldrepo:1.0
```

**networking**

`docker0`: a `bridge`

install `bridge-utils` to see details on it.

`brctl show docker0`

when you `ping` or do any `networking` operation from within `docker0` the packets go through the `docker0` bridge.   you can see it with `traceroute google.com`

in `Dockerfile`: `EXPOSE 80`: Makes it possible to expose port 80, we still need to run `-p 5020:80` localhost 5020 forwarded to 80 on container.

`docker port yourcontainer`: view exposed port.

by default ports are `tcp` if you want udp add it:

`docker run -d -p 5002:80/udp --name=yourcontainer apache`

attach a port to a different ip existing in host (for example if host has `eth0` and `eth1` each with its own ip):

`docker run -d -p 192.168.56.50:5003:80 --name=web3 apache-img`

`P`: capital p will automatically map the exposed ports from `Dockerfile` into random ports on host.

assign different address to `docker0` it has a specific network address it tries in case all are captured:

```bash
service docker stop
ip a
ip link del docker0
vi /etc/default/docker
  DOCKER_OPTS=--bip=150.150.0.1./24 # bip = bridge ip.
service docker start
```

any new container or old container that restarts will get an ip from the new range.

**linking containers**

safer because ports are not exposed.  we have a `src` and `rcvr` container the `src` when linked initially communicates with the `receiver` container and tells it what's its `EXPOSE` ports from the `Dockerfile` in this way the `receiver` container can communicate to these ports.  When linking containers the `--name` is important so that we link to this name.

`docker run --name=receiver --link=src:alias-src -it ubuntu:15.04 /bin/bash` you need to alias the `src`

what docker did is that in the `receiver` container docker updated the `hosts` file and `env variables` with the `src` container ip and port the `apps` that uses the other container need to use these environemnt variables in order to use the other container.

**troubleshooting containers**

```bash
service docker stop
docker -d -l debug &
```

or edit the `docker config file at /etc/default/docker` add to that file `DOCKER_OPTS=--log-level=debug`

* Start docker `daemon` in debug mode.

**When Dockerfile build fails**

```bash
docker images
```

you will see the latest successful image created, run it with `docker run -it 7e8yfushfjh /bin/bash` and try that command and troubleshoot why `Dockerfile` failed.




