# Dockerize Java And Mysql Example Project

![preview image](https://raw.githubusercontent.com/mixaverros88/docker-with-java-and-mysql/master/icons/docker_overview.JPG)

## Overview

This project is a simple example that demonstrates how to use Docker and Docker Compose to run a MySQL container alongside a Java application container.

The project includes two separate Dockerfiles:

- One for the **MySQL** service
- One for the **Java** application

Each Dockerfile is located in its respective directory (`mysql/` and `java/`). A `docker-compose.yml` file in the root directory is used to orchestrate and run both containers together.

Additionally, a `network/` directory is included, containing a shell script that creates a custom Docker network. This network allows the containers to communicate with each other using their container names.

***
### Docker Compose
At the root file run the following command 
```
docker-compose up
```
After visit the following url
```
http://localhost/RESTfulExample/rest/hello/Test
```
### Start the mysql container 

![preview image](https://raw.githubusercontent.com/mixaverros88/docker-with-java-and-mysql/master/icons/MySQL-512.png)
You can find the mysql dockerfile in the mysql folder. There you can run the build.sh file in order to build the docker image or you can run the following command.
```
docker build -t my-mysql .
```
If you want to change the database name you can change the ENV in dockerfile or you can remove it from docker file and pass the value at the run stage.

After the build of the image, you can run the image. You can use the run.sh file.
At the run stage you can specify the root pass, and the  ports.
Or you can run the below command
```
docker run -d -p 3311:3306 --name mysqlapp -e MYSQL_ROOT_PASSWORD=supersecret my-mysql
```

If you want to validate the creation of the database you can connect to the container with the below commands.

Connect in the running container 
```
docker exec -t my-mysql bash 
```
log in as a root
```
mysql -uroot -p
```
Root credentials
```
supersecret
```
Display all the databases
```
showdatabases;
```

Use the company database
```
use company;
```

Display all the tables of company database 
```
show tables;
```

Show all the rows of a employees 
```
select * from employees;
```
If the results are the following.

![preview image](https://raw.githubusercontent.com/mixaverros88/docker_with_java_and_mysql/master/icons/mysql_confirm.PNG)

The process is correct.
***

### Start the java container 

![preview image](https://raw.githubusercontent.com/mixaverros88/docker-with-java-and-mysql/master/icons/javaIco.png)
You can find the java dockerfile in the java folder. There you can run the build.sh file in order to build the docker image or you can run the following command.
```
docker build -t java-connect-with-mysql .
```
After the build of the image, you can run the image. You can use the run.sh file.
At the run stage you can specify the ports and the volumes.
Or you can run the below commands.
```
docker run  -d --name javaapp -p 80:8080 -v /$(pwd):/usr/local/tomcat/webapps java-connect-with-mysql
```
You can reach this service through docker machine http://docker-machine-ip/RESTfulExample/rest/hello/test

To find docker machine ip, execute the following command.
```
docker-machine ls
```
***
### Create network
![preview image](https://raw.githubusercontent.com/mixaverros88/docker_with_java_and_mysql/master/icons/docker_network.png) Create a bridge network in order to allow communication  between the containers, through container name instead of container IP.  
```
docker network create --driver bridge isolated 
```
Add db container into the network
```
docker network connect isolated db
```
Add java-connect-with-mysql container into the network
```
docker network connect isolated java-connect-with-mysql
```
***
### :computer: Deploy instructions ###
Use the maven plug-in (tomcat7-maven-plugin) for tomcat server in order to automatically deploy this project in your local server. You can find this plug-in in pom.xml file in order to modify with yours configurations, also don't forget to add in settings.xml your credentials for tomcat server.
```
<servers>
    <server>
        <id>TomcatServer</id>
        <username>admin</username>
        <password>password</password>
    </server>
</servers>
```
You can run the below command in order to deploy the artifact in your local tomcat server
```
mvn clean tomcat7:deploy
```


