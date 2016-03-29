#### psdockerforwebd
#####using docker machine
######
some commands
```
docker machine ls
docker machine start/stop/env/ip/status [machinename]
```

#####source code in docker
######hooking a volume to nodejs
```
docker rm -v 01     //delete container+value
```

our local machine has code in pwd, we want to run code in container machine, and set volume in /var/www/   
(image name=nodeimage) -w means change context(working dict in container)
```
docker run -p localport(8088):3000 -v $(pwd):/var/www -w "/var/www" nodeimage npm start
```
Then visit
```
192.168.x.x:8088 (dockerip:localbindip)
```
#####Build custom image
######build a nodejs image.
```
FROM nodeimage
MAINTAINER k
ENV NODE_ENV=production
COPY /src /var/www      //container
WORKDIR /var/www      //container
VOLUME["/var/www"]   //host
RUN npm install
EXPOSE 3000
ENTRYPOINT ["node","app.js"]
```
######
```
docker build -f othername -t rengokantai/name . //-t=--tag
```

#####commucating between containers
build first container:
```
docker run --name myname imagename1
```
build second container, link to container 1
```
docker run -d -p 80:80 --link myname:imagename1 imagename2
```
######link node to mongo
#some tricky

execute command in container. using hash as container name
```
docker exec 781231 node app.js
```

######create custom container network
```
docker network create --driver bridge networkname
```
run containers in container network
```
docker run -d --net=networkname --name containername imagename
docker run -d --net=networkname --name containername2 -p 3000:3000 imagename2
docker network ls
docker network inspect networkname
```

#####docker compose
first docker-compose.yml
```
version '2'
services:
  node:
    build:
      context: .
      dockerfile: node.dockerfile
    environment:
      - NODE_ENV=production
    ports:
      - "3000:3000"
    networks:
      - networkname
  mongodb:
    image: imgname
    networks:
      - networkname
networks:
  networkname
    driver:bridge
```
######comands
```
docker-compose build (build images)
up (start container)
down
logs
ps
stop
start
rm
```
Or build individual serv
```
dicker-compose build mongo
```
rerun, donot recreate services that node depends on
```
docker-compose up --no-deps node
```
stop and remove all containers
```
docker-compose down --rmi all --volumes
```

######managing dev env
remove all locked container
```
docker rm -f $(docker pa -a -q)
```

main crux:*****
create docker-compose.yml file, create multiple services, each service has xx.dockerfile file, may execute script using RUN command, then set env var, then docker-compose can get these env var





#####running in cloud
