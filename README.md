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
