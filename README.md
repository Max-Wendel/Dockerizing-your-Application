# Dockerizing your Application with docker-compose
- This is a simple tutorial to your add _docker_ and _docker-compose_ in your application.
**Obs: This tutorial run only Spring Boot projects, ok?**

## Let's go

## Create a Dockerfile
- First all, you need create a Dockerfile.
- This is a example of a simple Dockerfile which run your application:

```Dockerfile
FROM openjdk:8-jdk-alpine
VOLUME /tmp
COPY target/{JAR_FILE} app.jar
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-Dspring.profiles.active=docker","-jar","/app.jar"]
```

- Put in `{JAR_FILE}` you `.jar`, find in `{app.file}/target/..` (#1). **P.e: sample-app-0.0.1-SNAPSHOT.jar**

- Now, create a file `docker-compose.yml`, and write:
```yml
version: '3.2'

services:
  my-app:
    build:
      context: .
      dockerfile: Dockerfile
    image: my-app
    restart: always
    expose:
      - 8080
    ports:
      - 8090:8080
    dns: 8.8.8.8
```

- Run :
```sh
$ docker-compose up
```

## Database Example
- If you have a db connection in your application, this run your app (probably):

**Postgresql Example**

```yml
version: '3.2'

services:
  my-database:
    image: postgres:9.6.1
    ports:
      - "5433:5432"
    restart: always
    environment:
      - POSTGRES_DB=db
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=postgres

  my-app:
    build:
      context: .
      dockerfile: Dockerfile
    image: my-app
    restart: always
    expose:
      - 8080
    ports:
      - 8090:8080
    dns: 8.8.8.8
    environment:
      - SPRING_DATASOURCE_URL=jdbc:postgresql://my-database:5432/db
      - SPRING_DATASOURCE_USERNAME=postgres
      - SPRING_DATASOURCE_PASSWORD=postgres
      - SPRING_JPA_HIBERNATE_DDL-AUTO=create-drop
    depends_on:
      - my-database
```

## Run Yours Containers!
- Run the command below:

```sh
$ docker-compose up
```

## Done!

**#1: If you don't have a `.jar` in `/target`, run `mvn clean package` or `mvn clean install`**

**#2: To see your application run on terminal `sudo iptables -t nat -L -n`, and see where (host) you application already running**
