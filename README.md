1 Setting up
---

Run the 'Get Started' guide from <https://www.docker.com/> .


## 1.1 Under windows

We will do this workshop using the Docker Quickstart Terminal.

However, (for later, not now)
if you want to run it from the standard command line, you can do this by following these steps:
1. Make sure the Docker VM is running in VirtualBox
2. On the command line, run
``` FOR /f "tokens=*" %i IN ('docker-machine env') DO %i```
3. Run ```docker run hello-world``` on the command-line to verify that it is working.



### 1.2 Configuration on Windows

Follow this you want to run anything that is accessible from the outside.

**Configuring virtual box ports:**
 
Settings > Network (NAT) > Configure ports >


- Name: ssh
- Protocol: TCP
- Host IP: 127.0.0.1
- Host Port: \<exposed port>
- Guest Ip: 
- Guest Port: \<docker port>


For http we will configure it as following : 

- Name: ssh
- Protocol: TCP
- Host IP: 127.0.0.1
- Host Port: 6680
- Guest Ip: 
- Guest Port: 80

Now you can connect to the machine at:
 
    localhost:6680


**Running vm in background**

If you press left-shift while starting the vm, the vm will run headless.



2 Running Containers
---

## 2.1 Check whether things work

**Windows users:**
_Open up a Docker Quickstart Terminal (pressing your windows key and start typing
'terminal' should help you find it)._


To test whether docker is working, type:

```
docker run hello-world
```

This should notify you that Docker is working.


Now run
```
docker ps
```

That returns:

```
CONTAINER ID    IMAGE      COMMAND     CREATED STATUS      PORTS       NAMES
```

Which indicates you have no docker containers currently running.

_docker ps is like the Linux ps command and shows all running Docker
containers_

## 2.2 Running 'Hello Docker' and understanding what happens

Type:

```
docker run -it ubuntu /bin/echo "Hello Docker"

```
At the bottom of your output it shows:

*Hello Docker*

Nice huh?!

### 2.2.A What happened?


First, the image called '**ubuntu**' was pulled from the (local) dockerhub and you saw something like this.

```
maestro@mocker:~$ docker run -it ubuntu /bin/echo "Hello world"
Unable to find image 'ubuntu:latest' locally
Pulling repository ubuntu
07f8e8c5e660: Download complete
e9e06b06e14c: Download complete
a82efea989f9: Download complete
37bea4ee0c81: Download complete
Status: Image is up to date for sogeti:5000/ubuntu:latest
```
After that, Docker created a container from the image, fired it up,
attached a terminal to it, and ran

```
/bin/echo "Hello Docker"
```

exited the terminal, and stopped the container.


### 2.2.B The output explained further

The first two lines of the output are straightforward.

```
Unable to find image 'ubuntu:latest' locally
latest: Pulling from library/ubuntu
```

The image you are trying to start as a container, is not available. It
needs to be fetched from the repository. Note the Docker calls the complete path the repository.

Then there are 4(!) lines saying:

```
<some number> Download complete
```

Why? Well, Docker stores snapshots of images called _layers_, similar to
the way Git stores commits. These layers emerge in the creation process of
containers. We'll learn more when we start creating our own containers.

So the Docker image has been downloaded. That means if you repeat the command
we executed earlier, it will run be faster. Try it and observe how fast
it is.

To see the Docker images, type:

```
docker images
```

and

```
docker images -a
```

Explain the difference in output.


### 2.2.C The Docker run command line explained further

The _-i_ option opens a stdin to the container's running process and
_-t_ associates a pseudo TTY to the container. Both -i and -t are often used
together when you want to run a container in the foreground (i.e.
not detached). Docker containers are often run in detached mode rather than in the
foreground.

```/bin/echo "Hello Docker"``` is the command being executed by the Docker
container. Docker containers are often used to run one specified command,
typically a service. The command to run can be overridden on the command
line the way we did.

Now try to run the same command line but replace ```/bin/echo ...``` by
```/bin/bash``` like:

```
docker run -it --rm  ubuntu /bin/bash
```

( ```--rm``` removes the container upon exiting)

Notice that you are logged in to some terminal as root, indicated by:

```
root@<container id>/#
```

It appears that you have started a complete virtual machine. Wander
around, look at the directory tree, the hostname. It really feels like a
virtual machine. Is it actually one?

No not really. It is a little bit like an advanced way of doing chroot.
It feels like a virtual machine though. It has its own virtual file
system (which is actually a directory structure on the host, like
you'd get with chroot), it has its own virtual network interfaces
(observe that ifconfig yields different results than ifconfig on
your host).
So it feels like a virtual machine,
but a really lightweight one, without need for hypervisoring. It is just
a few processes in a, eh _container_.

To see what is running, open another terminal (ctrl-shift-n) and type:

(On windows: execute this in the Virtual Box docker image)

```
ps aux | grep bash
```




There are a few bash-es running but take a close look at these:

```
maestro   3049  0.0  1.1 116296 12184 pts/13   Sl+  18:27   0:00 docker run -it --rm sogeti:5000/ubuntu /bin/bash
root      3057  0.0  0.3  18180  3152 pts/24   Ss+  18:27   0:00 /bin/bash
```

(
In windows, only the bottom line will show, since I run the docker command
from the window command prompt, the docker command can be found in the windows task list:

```tasklist /V | findstr /R /C:"bash"```   )

There is a Docker process owned by you and a bash process owned by
root. Notice the similarities when you do 'ps aux | grep bash' in the
container.

So here we have a kind of virtualization that starts up instantly and is
really lightweight.

Exit the container with

```
 < ctrl-d >
```

to prepare for the next next exercise.

## 2.3 Running a detached container and inspecting it

Let's start the same command we saw earlier, but in a loop and detached.
Type on one line:

```
  docker run -d ubuntu  /bin/bash -c "while true; do echo \"Hello Docker\"; sleep 2; done"
```

The only output you get is a hash of the started container.

## 2.4 Looking at running containers
Look at:

```
docker ps
```

You will see your container running.

The name of your container is generated by Docker. In my case it is
**condescending_hypatia**.

## 2.5 Look at resources used.
Since Docker 1.5 it's possible to see the resources used by your running containers. For this the stats command was introduced. You use it like this:
```
docker stats condescending_hypatia
```


## 2.6 Looking at 'logs'

To see the output of your process, Docker logs attaches a standard out
to your terminal. This allows you to look at the logs. When using
Docker containers for processes, just logging to standard out is
often used for logging, as it integrates with Docker logs.

Try:

```
docker logs condescending_hypatia
```

(replace 'condescending_hypatia' with your container name)


it should show a whole list of:

```
...
Hello Docker
Hello Docker
Hello Docker
...
```

Adding the _-f_ option follows the log. Type a few enters to actually
see anything moving if your screen has already been filled with 'Hello Docker'
messages.

## 2.8 Stopping and starting the container

Open another terminal and type:

```
docker stop condescending_hypatia
```

(replace 'condescending_hypatia' with your container name)

After a few seconds you'll see that the container actually stops running.

Restarting the container can be done with

```
docker start condescending_hypatia
```

and

```
docker run -d ubuntu  /bin/bash -c "while true; do echo \"Hello Docker\"; sleep 2; done"
```

Try both and explain the difference with ```docker log``` and ```docker ps -a```.

## 2.9 Passing environment variables

We can pass environment variables to a Docker container with the ```-e```
option to the run command. For example:

```
docker run --rm -it -e MESSAGE="Hello Environment" ubuntu /bin/bash
```

passes the MESSAGE environment to the Docker container that starts a bash shell.
Go ahead and check this by using `env` or `echo $MESSAGE` to check
its existence in that shell.


## 2.10 Defining port forwards

We have a container with an Apache webserver. Let's fire it up:

```
 docker run -it --rm httpd:2.4

```

Verify that it is running with:

```
docker ps
```

Now open up a browser and browse to:

```
http://localhost:80/
```

It doesn't work, does it? The problem is that, while the web application is
listening to port 80, your host is not forwarding the port.

```
docker run -p <host:port>:<forward port> ....
```

forwards a port to your container. Now try and run the container
again while forwarding **80** on the docker host to **80** on your container
and try browsing to the web server again.

**NOTE for Windows users**: _Make sure there is a port mapping for virtual box to docker
 port 80. See paragraph **1.2**.

And Yay!!! it works. It shows the default Apache2 webpage.

Look at the output of `docker ps` and see the port forwarding in the
output.

There are several ways of having a network of containers working
together, as we will see later on. Using port forwaring and knowing the host on which you run is
one of them.

## 2.11 Defining a volume

"Where is my data ???" - sooner or later you'll ask yourself this
question when working with Docker containers. In a way we would say "why
should you care": it does not really matter where data resides, as long as it is
persistent and both your application and a backup system can access it,
you're fine.

One thing you do not want, is that by stopping a container and
running it again, you would destroy or hide your data.

Volumes comes to the rescue here.
Volumes can be defined in the Dockerfile as well as in the run command.
We will focus on defining them in the run command here.

With the run command you can use  ```-v``` to define a volume for a container.
Lets try.

Create a file 'data.txt' in the current (For windows: a subdirectory of _C:\Users_ )directory with some interesting
content. Run the container by typing (on one line):

```
docker run -it --rm -v `pwd`/data.txt:/var/data.txt ubuntu /bin/bash
```


and subsequently cat the mapped file:
```
cat /var/data.txt
```

This mounts your file as `/var/data.txt` in the container. You can also
mount a directory. In this way, data can survive the container.

## 2.12 Let's clean up our mess

### 2.12.A Clean up containers

First stop the container(s) that we have started. Make sure that
```
docker ps
```
yields and empty list. So there are no containers left.

But aren't there really?

Try:
```
docker ps -a
```

This shows all the containers we have started earlier with information that
they exited earlier.

```
docker stop <container hash or name>
```

stops a container and

```
docker rm <container hash or name>
```

deletes a container. Knowing that

```
docker ps -a -q
```

shows just the hashes of all containers, what would be a fast way to
remove all containers from your host?

Clean them up now, we'll move on to images.

### 2.12.C Cleaning up images

We've seen the `docker images (-a)` command. Docker images has a _-q_
option as well, which helps you to swiftly remove all images.

You should **not remove** the containers now! We can use these in the rest of the workshop.


# 3 Creating images

## 3.1 Exercise: define an image for a web application

### 3.1.A Create a webpage

1. Create an application directory: **website**
    **Note:** Windows users, do this in ```%USERPROFILE%\ ```

2. In that directory, create a file called ```index.html``` and give it a nice content, eg:
```HTML
<html>
<head>
    <title>My Docker HTTPD image</title>
</head>
<body>
    <h1>Welcome to the modified httpd docker image!</h1>
</body>
</html>
```

### 3.1.B Steps to define a Docker image for this web application:

- in the web application directory, create a file called ```Dockerfile```

- specify the base image from which your image derives

```
FROM httpd:2.4
```

- optionally, specify the author of the image

```
MAINTAINER your_name_goes_here
```


- copy all required files to the image

```
COPY index.html /usr/local/apache2/htdocs/
```



Now you can build the image using from the **website** directory:

```
docker build -t modified-httpd .
```

`modified-httpd` is the tag of your image.

Now run the image as a container with:

```
docker run -d -p 80:80 modified-httpd
```

And open chrome to ```http://localhost:<http port>``` (**Windows**: see **1.2** for the port mapping of port 80)

How can you see if the container is running? Take a look at the
container's logs.

Stop the container.


Off course, there is a lot more possible with Dockerfile. For a complete reference you can visit:
<https://docs.docker.com/engine/reference/builder/>



#5  Container orchestration


With Docker, you create containers that each encapsulate a single service or application.
When you divide a system into a bunch of collaborating services, the services need to be able to access each other in a convenient way.
Docker  provides the concept of _container linking_ for this.

## 5.1 Exercise: linking two containers

We are going to link Sonar container to a container that runs a database (Postgres)


First start sonar

```
    docker run -d --name sonarqube \
        -p 80:9000 -p 9092:9092 \
        -e SONARQUBE_JDBC_USERNAME=sonar \
        -e SONARQUBE_JDBC_PASSWORD=sonar \
        -e SONARQUBE_JDBC_URL=jdbc:postgresql://localhost/sonar \
        sonarqube
```

Go to ```localhost:<mapped port>``` ...
however, it will not open.

Why? Because it has no database. The postgresql db needs to be linked to the worpress image

Stop the sonar container
```
docker stop sonarqube
docker rm sonarqube
```

and start the Postgres database.

```
docker run -p 5432:5432 \
   --name sonardb \
   -e POSTGRES_PASSWORD=sonar \
   -e POSTGRES_USER=sonar \
   -e POSTGRES_DB=sonar \
   -d postgres
```

This creates a container that runs postgres at 5432.


Now start Sonar again, but this time link it to the database:


```
    docker run -d --name sonarqube \
        --link sonardb:mydb \
        -p 80:9000 -p 9092:9092 \
        -e SONARQUBE_JDBC_USERNAME=sonar \
        -e SONARQUBE_JDBC_PASSWORD=sonar \
        -e SONARQUBE_JDBC_URL=jdbc:postgresql://mydb/sonar  \
        sonarqube
```

Try it out. What happens? (takes a while, be patient and
check ```docker logs -f sonarqube``` to follow the logs)

The _--link_ option links another Docker container by name. That Docker
container will be accessible from within the other container through the
hostname _service_ (Docker modifies the hosts file in the container if
you run it with --link)
```


