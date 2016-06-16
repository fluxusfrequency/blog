getting started with docker on os x
http://viget.com/extend/how-to-use-docker-on-os-x-the-missing-guide

DOCKER

boot2docker runs docker daemon inside a VM bc docker daemon needs native Linux

brew install boot2docker
boot2docker init (does rsa with boot2docker server)
boot2docker up
boot2docker start (you should see env vars)
$(boot2docker shellinit >> ~/.bash_profile)
`src ~/.bash_profile` or `eval "$(boot2docker shellinit)"`

boot2docker status
boot2docker ip # docker Daemon host address
docker version

docker run foo
docker ps
docker stop foo # stopped != removed
docker rm foo

docker run foo /bin/bash # which prog to run in the container
docker run -d (daemonize, goes into background) # gives back container ID
docker logs container_name # get cont name from docker ps, shows stdout from container in local stdout
docker run -P map container ports to host (for viewing web apps) # -p 5000:5000 makes port mapping configurable
docker inspect appname # get low level info as JSON

docker rm


YOUR OWN IMAGES

docker images # all the local images (not containers)
docker pull # pull down an image before you try to use it

in a running container, make changes, then use SHA of container to make a new image locally
docker commit -m "add json" -a "fluxusfrequency" c695d85690fb fluxusfrequency/sinatra:v2

`echo .git >> .dockerignore` # don't check the history into the image

make a Dockerfile # only one process per container
```
FROM ubuntu:14.04
MAINTAINER Kate Smith <ksmith@example.com>
RUN apt-get update && apt-get install -y ruby ruby-dev
RUN gem install sinatra
```

Dockerfile commands: FROM, RUN, MAINTAINER, ENTRYPOINT, CMD, ENV, EXPOSE, ADD/COPY, VOLUME, USER, WORKDIR, ONBUILD



docker build -t ouruser/sinatra:v2 . # -t flag makes it belong to our user, . makes it find local Dockerfile
docker run -t -i ouruser/sinatra:v2 /bin/bash

docker push imagename # pushes to dockerhub
docker rmi imagename # delete it locally


DOCKER COMPOSE (formerly FIG)
brew install docker-compose

3 steps
1) write app Dockerfile
2) write docker-compose.yml # define all the containers
3) docker-compose up # runs it all

docker-compose up -d # daemonize
docker-compose run appname command
docker-compose ps
docker-compose stop




WERCKER

http://devcenter.wercker.com/learn/wercker-yml/sections.html

