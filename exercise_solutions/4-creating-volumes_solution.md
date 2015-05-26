---
# Defining volumes
---

## Exercise: create a volume 

###Solution: Dockerfile for Volume exercise

Create the Dockerfile in exercise/simple_node_service

```
ROM node-base
MAINTAINER Soget Dev
WORKDIR /opt/app
ADD node_modules /opt/app/node_modules
ADD service.js /opt/app/service.js
CMD ["/usr/bin/nodejs", "service.js"]
EXPOSE 8099
```

Build the image

```
docker build -t simple_node_service .
```

Run the image
```
docker run -d -p 8099:8099 -v /home/dockdev/exercises/simple_node_service/data:/var/data simple_node_service

```

Note that you have to provide an absolute path to the directory you are
mounting.

Use `curl` or a browser to send a request to the service. Does it work?


## Exercise: creating and using a data volume container

```
mkdir /var/tmp/data2
echo "another data source" > /var/tmp/data2/data.txt
docker create -v /var/tmp/data2/:/var/data --name mydata2 sogeti:5000/ubuntu /bin/true
docker run -d -p 8099:8099 --volumes-from mydata2 simple_node_service
```




