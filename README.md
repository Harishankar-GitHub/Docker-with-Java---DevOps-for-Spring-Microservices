# Docker with Java - DevOps for Spring Microservices

### :sparkles: Docker Introduction :sparkles:

##### Docker Intallation
```
If we have Windows Enterprise or Windows Pro
	-> Docker Desktop (https://docs.docker.com/engine/install/)
If we have Windows Home
	-> Docker Toolbox (https://docs.docker.com/toolbox/overview/)
	-> I use Docker Toolbox
```

##### To check the version of Docker
```
Open cmd -> docker --version
```

##### Quick Tip for Windows 10:
```
Use 192.168.99.100 in URL instead of localhost if you are using Windows 10 and are using docker toolbox.
NOTE: If 192.168.99.100 does not work, you can find the IP by using the command docker-machine ip

	Reason
		In Windows 10 when using docker toolbox , docker is configured to use
		the default machine with IP 192.168.99.100
```

##### First Docker Usecase - Deploying a Spring Boot Application

Open Docker Quickstart Terminal (also known as Docker Client) and execute the below commands
```
	docker --version
	docker run in28min/todo-rest-api-h2:1.0.0.RELEASE
	NOTE : 	The command is actually docker container run .....
			docker run .... is like a shortcut
```

##### Docker Registry - https://hub.docker.com/
##### Registry link for the application which we used earlier - https://hub.docker.com/r/in28min/todo-rest-api-h2

##### NOTE:
```
	Image is a static version
	Container is a running version of the image
	For the same image, we can have multiple containers running
```

##### Frequently used commands:
```
	Ctrl + C	-> To stop the container
	clear 		-> To clear the console
	docker run -p 5000:5000 in28min/todo-rest-api-h2:1.0.0.RELEASE
	-p 5000:5000 -> -p {HostPort}:{ContainerPort}
```

##### Why we use -p 5000:5000 ?
```
	By default, any container that we run is part of something called a bridge network in Docker.
	Bridge network is something like an Internal Docker Network.
	Nobody will be able to access it unless we specifically expose it onto the host, onto the system where we
	run the container.
	So, we are taking the internal port i.e., container port (5000) and mapping it to a host port (5000).
```
	
##### Once we run the application, we can use the following URLs to access it.
```
	http://192.168.99.100:5000/hello-world
	http://192.168.99.100:5000/hello-world-bean
	
	In the above command we can add -d
	docker run -p 5000:5000 -d in28min/todo-rest-api-h2:1.0.0.RELEASE
	-d stands for Detached mode
	By using this, we can run the container in the background.
	Once we start the application using the above command, an id is generated.
```

##### Few other important commands

- To see the logs -> docker logs id
- To tail the logs -> docker logs -f id
- To see what are the containers running -> docker container ls
- To see all images in my machine -> docker images
- To see all containers irrespective of their status -> docker container ls -a
- To stop a container -> docker container stop id (complete id or first few characters of id)

##### Pulling MySql official image from Docker Hub
```
docker pull mysql - this is official repository of mysql.
If we don't give the version, it will pull the "latest" tag from the repository
```

##### To search an image in registry
```
	docker search mysql
	It searches images which has mysql
```

##### To see the history of an image
```
	docker image history imageId
	instead of imageId, we can also use repositoryName:Tag
	Another command : docker history repositoryName:Tag
```

##### To inspect an image
```
	docker image inspect imageId
```

##### To remove an image from local (This command won't delete image from registry)
```
	docker image remove imageId
	If not able to remove, use the below command :
		docker rmi -f imageId
```

##### To pause/unpause the container
```
	docker container pause ID
	docker container unpause ID
```

##### To inspect a container
```
	docker container inspect ID
```
	
##### To remove all stopped containers
```
	docker container prune
```
	
##### To shutdown gracefully
```
	docker container stop ID
	It will wait for sometime to complete any ongoing process and then shutdown
	When using above command, a signal called SIGTERM is sent to container
	stop command is used mostly
```

##### To kill immediately
```
	docker container kill ID
	It will stop the container immediately
	When using above command, a signal called SIGKILL is sent to container
```

##### Restart Policy
```
	docker run -p 5000:5000 -d --restart=always in28min/todo-rest-api-h2:0.0.1-SNAPSHOT
	2 most popular values for restart policy -> always, no
	default is no
```

##### To see the events happening at the background
```
	docker events
```

##### To see what is the top process that is running in a specific container
```
	docker top containerId
```
	
##### To see the stats of the containers which are running
```
	docker stats
```

##### Defining Memory/CPU usage
```
docker run -p 5001:5000 -d -m 512m --cpu-quota 5000 in28min/todo-rest-api-h2:0.0.1-SNAPSHOT
In the above command, we can specify the maximum memory and maximum CPU usage
	10000 	-> 100%
	5000	-> 5%
```

##### To check what are the resources managed by docker deamon
```
docker system df
```

### :sparkles: Docker with Java Spring Boot Hello World Rest API :sparkles:

##### To build jar
```
	Right click on project -> Run As -> Maven Build -> Type "clean package" in Goals textbox and click Run.
	Instead of clean package, we can also use "package -DskipTests" or "clean package -DskipTests"
```
	
##### To build an image from a jar
```
	cd to the project folder
	docker build -t rhsb_docker/hello-world-rest-api:dockerfile1 .
	-t stands for Tag
	
	If using Windows 10 -> Need to enable "Expose Demon without TLS option"
```

##### To build jar from Terminal after adding plugin(s)
```
	In Docker terminal -> mvn package -DskipTests
```

##### To build jar from Eclipse after adding plugin
```
	Right click on project -> Run As -> Maven Build -> Type "package -DskipTests" in Goals textbox and click Run.
```

##### Plugins used to build Docker Images

- Building docker image manually
- Using Dockerfile to build image
- Using Dockerfile Spotify plugin to build image
- Creating generic reusable docker file
- Using JIB plugin to build image (Docker file not needed for JIB plugin)
- Using Fabric8 Docker Maven Plugin to build image

### :sparkles: Docker with Java Spring Boot Todo Web Application :sparkles:

##### To create a war file
```
	Right click project -> Run as -> Maven Build -> "clean package" -> Finish
```

##### Docker file to deploy .war file to Tomcat
```
	FROM tomcat:8.0.51-jre8-alpine
	EXPOSE 8080
	RUN rm -rf /usr/local/tomcat/webapps/*
	COPY target/*.war /usr/local/tomcat/webapps/ROOT.war
	CMD ["catalina.sh","run"]
```
##### Explanation of above docker file
```
	We need to deploy .war file in Tomcat. So, we are using Tomcat Base Image.
	Exposing in port 8080.
	Before copying .war file to Tomcat, we are removing the existing files (If any).
	Copying .war file to /usr/local/tomcat/webapps/ROOT.war because, this is the path thats needs to be used if we need to deploy in Tomcat.
	CMD ["catalina.sh","run"] - This is the shell file to launch up Tomcat.
```

##### Understanding ENTRYPOINT, CMD, COPY and ADD instructions
```
	COPY can be used with files, directories.
	We cannot specify a URL with COPY.
	We can use files, directories, URLs with ADD.
	It is recommended to use COPY when just copying files, directories.
	When using URLs, ADD can be used.
	
	CMD is used to pass parameters in docker run command.
	For example : docker run -p 8080:8080 imageName someCommand
	So, in docker file, we have specified a command inside CMD.
	That will be passed to the docker run command.
	After specifying a command in docker file, if we pass some other command in docker run command, the command specified in docker file will be overridden.
	Where as when a command is given in ENTRYPOINT, it will always execute and will not be overridden when a command is passed in docker run command.
	If --entrypoint is used instead of ENTRYPOINT, then it will be overridden when a command is passed in docker run command.
	
	When creating an application image, it is recommended to use ENTRYPOINT because, we will have some command(s) which needs to be run always and not to be overridden.
```

##### Creating account and pushing images to Docker Hub
```
	Dcoker Hub : https://hub.docker.com/
	Enter new Docker ID, Password and Email to Sign up.
	In Docker Desktop terminal / Docker Toolbox terminal, type docker login.
	Enter Username which is the Docker ID and password.
	Once logged in, type "docker push username/repositoryName:tagName"
	The image will be pushed to the docker hub account.
	
	We can also configure in the dockerfile-maven-plugin in pom.xml to push to docker hub whenever we build the application.
```