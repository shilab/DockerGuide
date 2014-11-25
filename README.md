A Guide to Using Docker
===========

###Installation
1. To install Docker for Mac go [here](https://github.com/boot2docker/osx-installer/releases/tag/v1.3.2), download boot2docker and run the installer.
2. Open up a terminal window and run the following commands:  

```
 $ boot2docker init
 $ boot2docker start  
 $ $(boot2docker shellinit)  
```  
You may see output like:

```
To connect the Docker client to the Docker daemon, please set:
    export DOCKER_HOST=tcp://192.168.59.103:2376
    export DOCKER_CERT_PATH=/Users/username/.boot2docker/certs/boot2docker-vm
    export DOCKER_TLS_VERIFY=1
```
Just copy and paste the commands into your terminal and you're ready to use Docker. 

###Running a Docker Image
To test your Docker install, lets run a Docker image.
In your terminal enter:  
```
docker run -i -t ubuntu /bin/bash
```  
You should see something like:  
```
root@1bb30bf675b8:/#
```  
You are now in an Ubuntu environment, and can use normal Linux commands. To get back to your terminal prompt just enter:  
```
exit
```  
###Building from a Dockerfile  
Docker images can be built from Dockerfiles. Open up your favorite text editor, create a file named Dockerfile containing the following:  

```  
###################################################
# Dockerfile for Matrix eQTL based on rocker/rstudio
##################################################

FROM rocker/rstudio

RUN install2.r --error --deps TRUE \
	MatrixEQTL 
```  
This Dockerfile uses the rocker/rstudio Docker image as a base image, and then installs the MatrixEQTL library.  

To build this Dockerfile type:  
```  
docker build -t meqtl .
```  
The name can only contain lowercase letters, numbers, underscores and periods. 
If all goes well you should see something like:  

```
Sending build context to Docker daemon  2.56 kB
Sending build context to Docker daemon 
Step 0 : FROM rocker/rstudio
 ---> 76ddb4b77948
Step 1 : RUN install2.r --error --deps TRUE 				MatrixEQTL
 ---> Running in 9639533265e0
trying URL 'http://cran.rstudio.com/src/contrib/MatrixEQTL_2.1.0.tar.gz'
Content type 'application/x-gzip' length 43020 bytes (42 Kb)
opened URL
==================================================
downloaded 42 Kb

* installing *source* package ‘MatrixEQTL’ ...
** package ‘MatrixEQTL’ successfully unpacked and MD5 sums checked
** R
** data
** demo
** inst
** preparing package for lazy loading
** help
*** installing help indices
*** copying figures
** building package indices
** testing if installed package can be loaded
* DONE (MatrixEQTL)

The downloaded source packages are in
	‘/tmp/downloaded_packages’
 ---> f548535d729f
Removing intermediate container 9639533265e0
Successfully built f548535d729f

``` 

To run your new Docker image type:  

```
docker run -d -p 8787:8787 meqtl
```

There should be one line of output which contains a mix of letters and numbers. This is the ID of the Docker process. If you type: 
```
docker ps
```
you should see something like:  

```
CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS                    NAMES
54fa643b3293        meqtl:latest        "/usr/bin/supervisor   50 seconds ago      Up 49 seconds       0.0.0.0:8787->8787/tcp   drunk_brattain4
```

To access the Rstudio interface you first need to find the IP address that Docker is using. You can easily find this by typing ```boot2docker ip```. 

Paste that IP address into your browser, along with a colon and the port the Docker image is listening too. It should look something like: 
```
192.168.59.103:8787```. 

If everything is working you should see a login page. ```rstudio``` is the default username and password. If you type ```library(MatrixEQTL)``` MatrixEQTL should be available to you.

###Stopping a Docker Container
You can specify a container to stop by using the container ID. So for our example you would and type:

```docker stop 54fa643b3293```

###Sharing your filesystem
If you want to share files between your computer and the Docker container you should use the ```-v``` flag. For example you can share you downloads directory with the RStudio container with the command:

```
docker run -v ~/Downloads:/home/rstudio/data -d -p 8787:8787 meqtl
```  
