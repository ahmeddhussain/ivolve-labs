## Lab 9: Containerized Node.js and MySQL Stack Using Docker Compose

## Overview
This lab demonstrates how to run a Node.js application and a MySQL database together using Docker Compose. The app depends on a database named `ivolve` and must be configured with environment variables for database access.

## Prerequisites
Before starting, make sure you have:
- Docker installed on your system
- Docker Compose installed
- Git installed on your system
- A Docker Hub account if you want to push the image

## Step 1: Clone the Application Code
Clone the repository:

```bash
git clone https://github.com/Ibrahim-Adel15/kubernets-app.git
cd kubernets-app
```
![alt text](<../screenshots/Pasted image (33).png>)
## Step 2: Review the Application Requirements
The application expects:
- a MySQL database named `ivolve`
- environment variables for database connection:
  - `DB_HOST`
  - `DB_USER`
  - `DB_PASSWORD`

Make sure the app is configured to use these values.

## Step 3: Create a Dockerfile for the App
Create a Dockerfile for the Node.js application that:
- uses a Node.js base image
- copies the source code into the container
- installs dependencies
- exposes port 3000
- starts the application

Example:

```dockerfile
FROM node:18-alpine
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3000
CMD ["npm", "start"]
```

Build the image locally:

```bash
docker build -t ahmedkhater2611/node-app:latest .
```
![alt text](<../screenshots/Pasted image (34).png>)
## Step 4: Create a Docker Compose File
Create a file named `docker-compose.yml` with the following structure:

```yaml
version: "3.9"
services:
  app:
    build:
      context: .
      dockerfile: Dockerfile
    ports:
      - "3000:3000"
    environment:
      DB_HOST: db
      DB_USER: root
      DB_PASSWORD: ivolve
    depends_on:
      - db

  db:
    image: mysql:8.0
    environment:
      MYSQL_ROOT_PASSWORD: ivolve
      MYSQL_DATABASE: ivolve
    volumes:
      - db_data:/var/lib/mysql

volumes:
  db_data:
  app_log:
```

## Step 5: Start the Stack
Run the services:

```bash
docker compose up -d
```

Verify the containers are running:

```bash
docker compose ps
```
![alt text](<../screenshots/Pasted image (35).png>)
## Step 6: Verify the Application
Check that the app is working by opening it in a browser or using curl:

```bash
curl http://localhost:3000
```
![alt text](<../screenshots/Pasted image (36).png>)
## Step 7: Verify Health and Readiness Endpoints
If the application exposes health and readiness routes, verify them with:

```bash
curl http://localhost:3000/health
curl http://localhost:3000/ready
```
![alt text](<../screenshots/Pasted image (37).png>)
## Step 8: Verify Application Logs
Check the application log directory inside the container:

```bash
docker compose exec app ls -R /app/logs
```

You can also view the application logs directly:

```bash
docker compose logs app
```
![alt text](<../screenshots/Pasted image (38).png>)
## Step 9: Push the Image to Docker Hub
Log in to Docker Hub:

```bash
docker login 
```
You will be prompted for the username & password after that, so enter them.

Push the image:

```bash
docker push ahmedkhater2611/node-app:latest
```
![alt text](<../screenshots/Pasted image (39).png>)
## Step 10: Clean Up
Stop and remove the containers:

```bash
docker compose down
```

To remove the database volume as well:

```bash
docker volume rm kubernets-app_db_data
```

## Notes
- The MySQL container creates the `ivolve` database automatically when `MYSQL_DATABASE=ivolve` is set.
- The app service should wait for MySQL to become ready before starting.
- Health and readiness endpoints may vary depending on the application code.

## Conclusion
This lab shows how to containerize a Node.js application with MySQL using Docker Compose and how to verify the application, health endpoints, logs, and image publishing.
