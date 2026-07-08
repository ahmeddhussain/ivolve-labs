## Lab 3: Run Java Spring Boot App in a Container

## Overview
This lab shows how to containerize a Java Spring Boot application using Docker. You will clone the project, create a Dockerfile, build a container image, run the container, and test the application.

## Prerequisites
Before starting, make sure you have:
- Docker installed on your system
- Git installed on your system

## Step 1: Clone the Application Code
Clone the repository:

```bash
git clone https://github.com/Ibrahim-Adel15/Docker-1.git
cd Docker-1
```


## Step 2: Write the Dockerfile
Create a Dockerfile that:
- uses a Maven base image with Java 17
- creates a working directory
- copies the application source code into the container
- builds the app using mvn package
- runs the jar file from target/demo-0.0.1-SNAPSHOT.jar
- exposes port 8080

Example:

```dockerfile
FROM maven:3.9.9-eclipse-temurin-17
WORKDIR /app1
COPY . .
RUN mvn package
EXPOSE 8080
CMD ["java", "-jar", "target/demo-0.0.1-SNAPSHOT.jar"]
```

And to make a better layered Dockerfile:
```dockerfile
FROM maven:3.9.9-eclipse-temurin-17
WORKDIR /app
RUN adduser --system --group appuser
COPY pom.xml .
RUN mvn dependency:go-offline
COPY src ./src
RUN mvn package
USER appuser
EXPOSE 8080
CMD ["java", "-jar", "target/demo-0.0.1-SNAPSHOT.jar"]
```
This would run the container as non-root for better security and split the dependency download so it would be cached and not run every time.

## Step 3: Build the app1 Image
Build the Docker image:

```bash
docker build -t app1 .
```

Note the image size after the build.
![alt text](<../screenshots/Pasted image.png>)
![alt text](<../screenshots/Pasted image (2).png>)

 4: Run the Container
Run the container from the image:

```bash
docker run -d -p 8080:8080 --name container1 app1
```

![screenshots/Pasted image (3).png](<../screenshots/Pasted image (3).png>)


## Step 5: Test the Application
Open the application in a browser or use curl to verify it is running:

```bash
curl http://localhost:8080
```

![!\[!\\[Screenshot: Capture the successful application response.\\](<screenshots/Screenshot 2026-07-08 000005.png>)\](<screenshots/Pasted image (4).png>)](<../screenshots/Pasted image (4).png>)

## Step 6: Stop and Delete the Container
Stop and remove the container when you finish:

```bash
docker stop container1
docker rm container1
```

## Notes
- Make sure port 8080 is free before running the container.
- If the application does not start, check the container logs using docker logs container1.
- Make .dockerignore to not include the Dockerfile itself inside the image.

---


