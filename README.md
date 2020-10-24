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
						 or docker stop id (complete id or first few characters of id)

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
	
	Or docker stop id1 id2 -> To remove multiple containers
	id1 and id2 are first few characters of container's id.
```

##### To kill immediately
```
	docker container kill ID
	It will stop the container immediately
	When using above command, a signal called SIGKILL is sent to container
```

##### To remove a specific stopped container
```
	docker rm containerName or containerId
	docker rm id1 id2 -> To remove multiple containers
	id1 and id2 are first few characters of container's id.
```

##### Restart Policy
```
	docker run -p 5000:5000 -d --restart=always in28min/todo-rest-api-h2:0.0.1-SNAPSHOT
	2 most popular values for restart policy -> always, no
	default is no
```

##### To restart a container
```
	docker container restart containerId
	or docker container restart containerName
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

### :sparkles: Docker with Java Spring Boot Todo Web Application using MySQL :sparkles:

##### Connecting MySql to the application
```
	Usual method -> Downloading MySql in our machine and connecting the application.
	Using Docker -> From Docker, we can pull the official image of MySql and run it from docker.
	We can search mysql in Docker Hub and pull the required version.
	
	For example : docker run mysql:5.7
	In the above command, we are pulling the official mysql image (Version 5.7).
	We can use docker pull mysql:5.7. But, docker run will pull and run the image if it is not present in our machine.
```

##### Command to run mysql from docker
```
	docker run -d -e MYSQL_ROOT_PASSWORD=dummy -e MYSQL_DATABASE=todos -e MYSQL_USER=todos-user -e MYSQL_PASSWORD=dummy mysql:5.7
```
```
	Explanation for above command:
		-d -> running in detached mode, so that, terminal is available to execute any other commands when mysql is running.
		-e -> configuring environment variables
		Configuring values -> MYSQL_ROOT_PASSWORD, MYSQL_DATABASE, MYSQL_USER, MYSQL_PASSWORD
		
	Once the mysql started running, we need to open MySql Shell.
	MySql Shell has to be downloaded and installed. If we get an error like vcruntime140_1.dll is missing,
	we need to download the file from google and keep it in mysqlsh.exe path.
	
	Once the above requirements are satisfied, double click on mysqlsh.exe file to open MySql Shell.
	To connect to database, type
		\connect todos-user@localhost:3306 or \connect todos-user@192.168.99.100:3306
		\connect MYSQL_USER@localhost:PortNumber of MySql which is running in docker.
		
		Note: Default port of MySql is 3306
		
	After this, it will ask for password.
	But, we will get error saying cannot connect to localhost...
	That is because, in the above docker run command, we did not map the port of mysql image to outside world.
	
	Here's the proper command which exposes port:
	docker run -d -e MYSQL_ROOT_PASSWORD=dummy -e MYSQL_DATABASE=todos -e MYSQL_USER=todos-user -e MYSQL_PASSWORD=dummy --name mysql -p 3306:3306 mysql:5.7
	--name mysql is used to give a name to the container
	
	After this, we can successfully connect MySql Docker Image from MySql Shell.
```
```
	After connecting MySql Docker Image from MySql Shell,
	we can use the below commands to work.
	
	\sql - Switching to SQL mode
	use databaseName - To work on a particular database
	select * from tableName;
```

##### Running Web Application and MySql from Docker

To run MySql
```
	docker run -d -e MYSQL_ROOT_PASSWORD=dummy -e MYSQL_DATABASE=todos -e MYSQL_USER=todos-user -e MYSQL_PASSWORD=dummy --name mysql -p 3306:3306 mysql:5.7
	This command is same as the one that is in the above section.
```
To run Web Application:
```	
	docker run -p 8080:8080 --link=mysql rhsb/todo-web-application-mysql:0.0.1-SNAPSHOT
	--link is used to connect Web Application with MySql
```

##### Connecting Web Application with MySql - Using Link
```
	docker run -p 8080:8080 --link=mysql rhsb/todo-web-application-mysql:0.0.1-SNAPSHOT
	
	In the above command, we have used --link to connect the application with MySql.
	Note: --link is deprecated
```

##### Docker Networking

There are 3 types of networks:
```
	- Bridge
	- Host
	- None
```

By default, the containers are launched into the Bridge network.

Command to check the types of networks
```
	docker network ls
```
To inspect Bridge network
```
	docker inspect bridge or
	docker inspect networkId of bridge
```

##### Connecting Web Application with MySql - Using Host
```
	In bridge network, an internal network is created inside docker. So, we use (-p 8080:8080) to expose it.
	When launching containers in host network, we need not specify the port.
	
	Command to launch containers using host network:
		docker run -d --network=host rhsb/todo-web-application-mysql:0.0.1-SNAPSHOT
	
	Host is like a virtual machine. Web application and MySql will be able to connect. But, we cannot access
	the application from browser.

	The host networking driver only works on Linux hosts, and is not supported on Docker Desktop for Mac,
	Docker Desktop for Windows, or Docker EE for Windows Server.
	Reference: https://docs.docker.com/network/host/
	
	If deploying something in cloud, then we can use host networking.
```

##### Connecting Web Application with MySql - Using None
```
	We can use the below command for None network
		docker run -d --network=none rhsb/todo-web-application-mysql:0.0.1-SNAPSHOT
	
	By using this, the container will not have any network at all.
	So, this is not the option. We don't want the containers to have no network.
```

##### Connecting Web Application with MySql - Using Custom Network
```
	We can create our own network.
	Make the containers part of it.
	Now, we can make applications communicate with each other.
```
To create a Custom Network:
```
	docker network create anyCustomNetworkName
	For example: docker network create web-application-mysql-network
```
Running containers using a custom network:
```
	Running MySql in custom network:
	docker run -d -e MYSQL_ROOT_PASSWORD=dummy -e MYSQL_DATABASE=todos -e MYSQL_USER=todos-user -e MYSQL_PASSWORD=dummy --name mysql -p 3306:3306 --network=web-application-mysql-network mysql:5.7
	
	Running Web application in custom network:
	docker run -d -p 8080:8080 --network=web-application-mysql-network rhsb/todo-web-application-mysql:0.0.1-SNAPSHOT
```

##### Using Docker Volumes to Persist Data

```
	Web Application container can be launched using the usual command (As mentioned in the above section).
	We need to persist/store data in the database even if we stop or restart the mysql container.
	So, we use a volume only in the mysql container to persist/store the data.
```
Command to run mysql container with a volume
```
	docker run -d -e MYSQL_ROOT_PASSWORD=dummy -e MYSQL_DATABASE=todos -e MYSQL_USER=todos-user -e MYSQL_PASSWORD=dummy --name mysql -p 3306:3306 --network=web-application-mysql-network --volume mysql-database-volume:/var/lib/mysql mysql:5.7
```

Explanation for the above command
```
	Command to create a volume: --volume mysql-database-volume:/var/lib/mysql
	We are creating a volume with the name mysql-database-volume and storing the data that is inside
	this folder (/var/lib/mysql).
	/var/lib/mysql -> This is the default folder/path inside mysql container where the data is stored.
```

Few commands to play with Volume
```
	To view the list of volumes: docker volume ls
	To inspect a volume: docker volume inspect volumeid or volumeName
```

### :sparkles: Docker with Java Spring Boot React Full Stack Application :sparkles:

##### Running the Back end application
```
	docker run -d -p 8080:8080 rhsb/rest-api-full-stack:0.0.1-SNAPSHOT
```

##### Running the Front end application
```
	Open the code in Visual Studio Code
	Install the dependencies: npm install
	Start the application: npm start
```

##### To create a production build
```
	In Visal Studio Code: npm run build
```

##### Creating Multi Stage Docker Build for React Frontend Code
```
	Refer the dockerfile inside the front end project
```

##### To build an image in Docker Toolbox
```
	Navigate to the project folder from Docker Toolbox.
	Use the command: docker build .
```

##### Improve Front End Docker Build - dockerignore
```
	Creating a .dockerignore file inside front end project.
	Adding node_modules in the .dockerignore file so that it won't be sent to docker daemon.
	After saving .dockerignore file, run the below command
	docker build . -t rhsb/todo-front-end:0.0.1-SNAPSHOT
```

##### Other information
```
	nginx exposes in 80 by default.
```

##### Running the front end application after creating image
```
	docker run -p 4200:80 rhsb/todo-front-end:0.0.1-SNAPSHOT
```

##### Using Multi Stage Docker Build for Java REST API Backend
```
	Refer the dockerfile inside the back end project
	Command to run in docker toolbox for building image: docker run .
	Once it is run, an image would be created.
	But it won't have any name as we didn't configure it.
	We can assign a tagName to that image using the following command.
		docker tag 1cbce00caac7 rhsb/rest-api-full-stack:2stagebuild
	To run: docker run -p 8080:8080 rhsb/rest-api-full-stack:2stagebuild
```

##### Docker Compose

Docker compose is used to launch up multiple containers at a time/simultaneously.
It is also used to associate resources/volumes, network etc.

```
	Documentation: https://docs.docker.com/compose/
```

To check the docker-compose version
```
	docker-compose -version
```

docker-compose.yml file
```
	Refer docker-compose.yml file present inside the Fullstack application folder
```

Docker Compose Scale Command
```
	docker-compose up
	docker-compose up -d -> To launch in detached mode
	Ctrl+C -> Will stop the containers.
	docker-compose down -> Will stop the applications and delete the network
```

To view the details of an image
```
	docker images imageName or imageId
```

##### Using Docker Compose for Java Spring Boot Todo Web Application - MySql
```
	Refer docker-compose.yml file inside the application
```

##### Playing with Docker Compose

- docker-compose up -d
- docker-compose build
- docker-compose events -> To see the events that are happening with docker-compose
- docker-compose config -> To see the configuration
- docker-compose images -> To see the images that are used inside docker-compose
- docker-compose ps -> To see the processes, containers, command, state etc.
- docker-compose top -> To see the processes running in each of the containers
- docker-compose pause -> To pause all the containers inside docker-compose
- docker-compose unpause -> To unpause all the containers inside docker-compose
- docker-compose stop -> To stop the containers inside inside docker-compose
- docker-compose kill -> To kill the containers inside inside docker-compose
- docker-compose rm -> To remove all the stopped containers

### :sparkles: Java Spring Boot Microservices :sparkles:

##### To create images for CES and CCS Applications

- Open 2 Docker Terminals.
- cd to Microservices folder in both terminals.
- In 1 terminal, navigate to currency-exchange-service.
- In another terminal, navigate to currency-conversion-service.
- mvn clean package in both terminals to build images.
- If not able to do from Docker Terminal, from IDE we can create image.
	Right click on project -> Run As -> Maven Build -> Type "clean package" in Goals textbox and click Run.

- After this, open another terminal and cd till Microservices folder.
- This terminal is to configure network, run etc.
- The above 2 terminals are to build images for CES and CCS applications.

```
	Creating network: docker network create currency-network
```
##### Launching applications
```
	Launching container for Currency Exchange Service
		docker run -d -p 8000:8000 --network=currency-network --name=currency-exchange-service rhsb/currency-exchange-service:0.0.1-SNAPSHOT
		
	Launching container for Currency Conversion Service
		docker run -d -p 8100:8100 --network=currency-network -e CURRENCY_EXCHANGE_URI=http://currency-exchange-service:8000 --name=currency-conversion-service rhsb/currency-conversion-service:0.0.1-SNAPSHOT
```

##### Running the applications using Docker Compose
```
	Refer docker-compose.yml file
```
