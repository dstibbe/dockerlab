1 Setting up
---

Run the 'Get Started' guide from <https://www.docker.com/> .


## Under windows

We will do this workshop using the Docker Quickstart Terminal.

However, (for later, not now)
if you want to run it from the standard command line, you can do this by following these steps:
1. Make sure the Docker VM is running in VirtualBox
2. On the command line, run
``` FOR /f "tokens=*" %i IN ('docker-machine env') DO %i```
3. Run ```docker run hello-world``` on the command-line to verify that it is working.



### Configuration on Windows

Follow this you want to run anything that is accessible from the outside.

**Configuring virtual box ports:**
 
Settings > Network (NAT) > Configure ports >


- Name: ssh
- Protocol: TCP
- Host IP: 127.0.0.1
- Host Port: \<exposed port>
- Guest Ip: 
- Guest Port: \<docker port>

Now you can connect to the machine at:
 
    localhost:\<exposed port>


**Running vm in background**

If you press left-shift while starting the vm, the vm will run headless.
