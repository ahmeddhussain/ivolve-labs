## Lab 5: Multi-Stage Build for a Node.js App

## Overview
This lab demonstrates how to create a smaller and more efficient Docker image using a multi-stage build. The first stage builds the application, and the second stage copies only the final artifact into a lighter runtime image.

## Prerequisites
Before starting, make sure you have:
- Docker installed on your system
- Git installed on your system
- Internet access to clone the repository

## Step 1: Clone the Application Code
Clone the repository:

```bash
git clone https://github.com/Ibrahim-Adel15/Docker-1.git
cd Docker-1
```

## Step 2: Write a Dockerfile with Multi-Stage Build
Create a Dockerfile that:
- uses a Maven base image for the first stage
- copies the application code into the container
- builds the app using `mvn package`
- uses a Java base image for the second stage
- copies the JAR file from the first stage
- exposes port 8080
- runs the application

Example:

```dockerfile
FROM maven:3.9.9-eclipse-temurin-17 AS build
WORKDIR /src
COPY . .
RUN mvn clean package

FROM eclipse-temurin:17-jre
WORKDIR /app
COPY --from=build /src/target/demo-0.0.1-SNAPSHOT.jar app.jar
EXPOSE 8080
CMD ["java", "-jar", "app.jar"]
```

## Step 3: Build the app3 Image
Build the Docker image:

```bash
docker build -t app3 .
```

Note the image size after the build.
![alt text](<../screenshots/Pasted image (10).png>)
![alt text](<../screenshots/Pasted image (11).png>)

## Step 4: Run the Container
Run the container from the image:

```bash
docker run -d -p 8080:8080 --name container3 app3
```
![alt text](<../screenshots/Pasted image (12).png>)

## Step 5: Test the Application
Open the application in a browser or use curl to verify it is running:

```bash
curl http://localhost:8080
```
![screenshots/Pasted image (13).png](<../screenshots/Pasted image (13).png>)


## Step 6: Stop and Delete the Container
Stop and remove the container when you finish:

```bash
docker stop container3
docker rm container3
```

## Step 7: Delete the Image
Remove the Docker image:

```bash
docker rmi app3
```

## Notes
- Multi-stage builds help reduce the final image size.
- Make sure port 8080 is free before running the container.
- Only the Jar file would be inside the container as shown in the screenshot above.