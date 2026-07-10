## Lab 8: Custom Docker Network for Microservices

## Overview
This lab demonstrates how to create a custom Docker network for communication between a frontend and backend service in a microservices setup.

## Prerequisites
Before starting, make sure you have:
- Docker installed on your system
- Git installed on your system


## Step 1: Clone the Application Code
Clone the repository:

```bash
git clone https://github.com/Ibrahim-Adel15/Docker5.git
cd Docker5
```

## Step 2: Write the Backend Dockerfile
Create a Dockerfile for the backend service that:
- uses a Python base image
- installs Flask
- exposes port 5000
- runs `python app.py`

Example:

```dockerfile
FROM python:3.11-slim
WORKDIR /app
RUN pip install flask
COPY . .
EXPOSE 5000
CMD ["python", "app.py"]
```

Build the backend image:

```bash
docker build -t backend-image .
```
![alt text](<../screenshots/Pasted image (27).png>)
## Step 3: Write the Frontend Dockerfile
Create a Dockerfile for the frontend service that:
- uses a Python base image
- installs packages from `requirements.txt`
- exposes port 5000
- runs `python app.py`

Example:

```dockerfile
FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .
EXPOSE 5000
CMD ["python", "app.py"]
```

Build the frontend image:

```bash
docker build -t frontend-image .
```
![alt text](<../screenshots/Pasted image (28).png>)
## Step 4: Create a Custom Docker Network
Create a new Docker network with a custom subnet:

```bash
docker network create --subnet=192.168.10.0/24 ivolve-network
```

Verify the network:

```bash
docker network ls
``` 
![alt text](<../screenshots/Pasted image (29).png>)
## Step 5: Run the Backend Container
Run the backend container on the custom network:

```bash
docker run -d --name backend1 --network ivolve-network -p 5001:5000 backend-image
```

## Step 6: Run the Frontend Container on the Custom Network
Run a frontend container connected to the custom network:

```bash
docker run -d --name frontend1 --network ivolve-network -p 5002:5000 frontend-image
```

## Step 7: Run Another Frontend Container on the Default Network
Run another frontend container on the default bridge network:

```bash
docker run -d --name frontend2 -p 5003:5000 frontend-image
```
![alt text](<../screenshots/Pasted image (30).png>)
## Step 8: Verify Container Communication
Check the running containers:

```bash
docker ps
```

Use `docker exec` and requests package to test connectivity between the containers:

```bash
docker exec frontend1 python -c "import requests; print(requests.get('http://backend1:5000').text)"
```
![!\[alt text\](<../screenshots/Pasted image (31).png>)](<../screenshots/Pasted image (32).png>)

Frontend1 container reaches the backend container because they are on the same network, while Frontend2 container gets stuck and can't reach the backend container beacuse it's on the default network.

## Step 9: Delete the Custom Network
Remove the containers first:

```bash
docker rm -f backend1 frontend1 frontend2
```

Then delete the custom network:

```bash
docker network rm ivolve-network
```

## Notes
- Containers on the same custom network can communicate using container names as hostnames.
- Containers on different networks cannot communicate unless you connect them explicitly.
- A custom subnet is useful when you want to segment services logically.

## Conclusion
This lab shows how to isolate and connect microservices using a custom Docker network.
