# Spring Boot Microservices

#### Docker Commands

##### Start MySql Container (downloads image if not found)

`docker run  --detach   --name ec-mysql -p 6604:3306 -e MYSQL_ROOT_PASSWORD=password -e MYSQL_DATABASE=explorecali -e MYSQL_USER=cali_user -e MYSQL_PASSWORD=cali_pass -d mysql`

##### view all images

`docker images`

##### view all containers (running or not)

`docker ps -a`

##### Interact with Database (link to ec-mysql container) with mysql client

`docker run -it --link ec-mysql:mysql --rm mysql sh -c 'exec mysql -h"$MYSQL_PORT_3306_TCP_ADDR" -P"$MYSQL_PORT_3306_TCP_PORT" -uroot -p"$MYSQL_ENV_MYSQL_ROOT_PASSWORD"'`

##### Stop ec-mysql container

`docker stop ec-mysql`

##### (ReStart) ec-mysql container

`docker start ec-mysql`

##### Remove ec-mysql container (must stop it first)

`docker rm ec-mysql`

##### Remove image (must stop and remove container first)

`docker rmi mysql:latest`

#

### Startup with Profile settings

##### Default profile, H2 database

``
mvn spring-boot:build-image -DskipTests

``

##### mysql profile, MySql database (requires running container ec-mysql)

``
mvn spring-boot:build-image -DskipTests

``

#### Dockerize Explore California

##### Build jar, image, set default profile

`mvn spring-boot:build-image -DskipTests`

###### container with default property set in Dockerfile

`docker run --name ec-app-default -p 8080:8080  -d mabeatti/mmv2-explorecali-default:3.0.0-SNAPSHOT`

##### Build jar, image, set mysql profile

`set mysql in applications.properties`

##### Run Docker container with mysql profile

`docker run    --name ec-app-mysql -p 8181:8080  --link ec-mysql:mysql -d mabeatti/mmv2-explorecali-mysql:3.0.0-SNAPSHOT`

##### Build jar, image, set docker profile

`set docker in applications.properties`

##### Run Docker container with docker profile set in Dockerfile and migration scripts on host

`docker run --name ec-app-docker -p 8282:8080 -v ~/db/migration:/var/migration -e server=ec-mysql -e port=3306 -e dbuser=cali_user -e dbpassword=cali_pass --link ec-mysql:mysql -d mabeatti/mmv2-explorecali-docker:3.0.0-SNAPSHOT`

##### Enter Docker container

`docker exec -t -i ec-app /bin/bash`

#####

##### Push image to Docker hub

######Login to Docker hub locally
`docker login`

###### Upload image

`docker tag <image id> <docker hub repository>/explorecali-default:latest`

###### Download image

`docker pull <docker hub repository>/explorecali-default`

##### Run Container from docker hub image

`
docker run --name ec-app-default -p 8080:8080  -d <docker hub repository>/explorecali-default`
