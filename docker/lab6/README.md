## Lab 6: Managing Docker Environment Variables Across Build and Runtime

## Overview
This lab demonstrates how to manage environment variables in Docker in three different ways:
- passing variables directly in the docker run command
- passing variables from a file using `--env-file`
- defining variables inside the Dockerfile

## Prerequisites
Before starting, make sure you have:
- Docker installed on your system
- Git installed on your system
- Internet access to clone the repository

## Step 1: Clone the Application Code
Clone the repository:

```bash
git clone https://github.com/Ibrahim-Adel15/Docker-3.git
cd Docker-3
```

## Step 2: Write the Dockerfile
Create a Dockerfile that:
- uses a Python base image
- installs Flask
- exposes port 8080
- runs the application using `python app.py`

Example:

```dockerfile
FROM python:3.11-slim
WORKDIR /app
RUN pip install flask
COPY . .
EXPOSE 8080
CMD ["python", "app.py"]
```

## Step 3: Build the Docker Image
Build the image:

```bash
docker build -t app6 .
```

Note the image size after the build.
![!\[alt text\](<../screenshots/Pasted image (14).png>)](<../screenshots/Pasted image (14).png>)

## Step 4: Run the First Container
Run the first container and pass the environment variables directly in the command:

```bash
docker run -d -p 8081:8080 --name container6a -e APP_MODE=development -e APP_REGION=us-east app6
```
![alt text](<../screenshots/Pasted image (15).png>)
## Step 5: Run the Second Container Using an Environment File
Create a file named `envfile` with the following content:

```bash
APP_MODE=staging
APP_REGION=us-west
```

Run the container using the file:

```bash
docker run -d -p 8082:8080 --name container6b --env-file envfile app6
```
![alt text](<../screenshots/Pasted image (16).png>)
![alt text](<../screenshots/Pasted image (17).png>)
## Step 6: Run the Third Container Using Dockerfile Variables
Update the Dockerfile to define the following environment variables:

```dockerfile
FROM python:3.11-slim
WORKDIR /app
RUN pip install flask
COPY . .
ENV APP_MODE=production
ENV APP_REGION=canada-west
EXPOSE 8080
CMD ["python", "app.py"]
```

Rebuild the image:

```bash
docker build -t app6 .
```
![!\[!\\[!\\\[alt text\\\](<../screenshots/Pasted image (18).png>)\\](<../screenshots/Pasted image (17).png>)\](<../screenshots/Pasted image (20).png>)](<../screenshots/Screenshot 2026-07-08 202343.png>)
Run the container:

```bash
docker run -d -p 8083:8080 --name container6c app6
```
![alt text](<../screenshots/Pasted image (19).png>)
## Step 7: Test the Application
You can verify each container is running by using curl or opening the app in a browser:

```bash
curl http://localhost:8081
curl http://localhost:8082
curl http://localhost:8083
```

## Step 8: Stop and Delete the Containers
Stop and remove the containers when you finish:

```bash
docker stop container6a container6b container6c
docker rm container6a container6b container6c
```

## Step 9: Delete the Image
Remove the Docker image:

```bash
docker rmi app6
```

## Notes
- Environment variables can be passed at runtime using `-e` or `--env-file`.
- Variables defined in the Dockerfile are used by default unless overwritten at runtime.

