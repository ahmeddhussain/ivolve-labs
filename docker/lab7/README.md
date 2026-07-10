## Lab 7: Docker Volume and Bind Mount with Nginx

## Overview
This lab demonstrates how to use both a Docker volume and a bind mount with an Nginx container. The volume is used to persist Nginx logs, while the bind mount is used to share a local HTML page inside the container.

## Prerequisites
Before starting, make sure you have:
- Docker installed on your system


## Step 1: Create a Docker Volume for Nginx Logs
Create a named volume to store Nginx logs:

```bash
docker volume create nginx_logs
```

Verify the volume exists and inspect its default storage location:

```bash
docker volume inspect nginx_logs
```

On Linux, the default volume path is usually:

```bash
/var/lib/docker/volumes/nginx_logs/_data
```
![alt text](<../screenshots/Pasted image (20).png>)
## Step 2: Create a Local HTML File for the Bind Mount
Create a folder on your local machine and add an HTML file:

```bash
mkdir -p ~/nginx-bindmount
cat > ~/nginx-bindmount/index.html <<'EOF'
<!DOCTYPE html>
<html>
  <head>
    <title>Nginx Bind Mount</title>
  </head>
  <body>
    <h1>Hello from Bind Mount</h1>
  </body>
</html>
EOF
```
![alt text](<../screenshots/Pasted image (21).png>)
## Step 3: Run the Nginx Container
Run a container with:
- a volume mounted to `/var/log/nginx`
- a bind mount mounted to `/usr/share/nginx/html`

```bash
docker run -d --name nginx-lab7 -p 8080:80 \
  -v nginx_logs:/var/log/nginx \
  -v ~/nginx-bindmount:/usr/share/nginx/html \
  nginx:latest
```
![alt text](<../screenshots/Pasted image (22).png>)
## Step 4: Verify the Nginx Page
Use `curl` from your local machine to verify the page:

```bash
curl http://localhost:8080
```
![alt text](<../screenshots/Pasted image (23).png>)
You should see the content from your local `index.html` file.

## Step 5: Update the HTML File and Verify Again
Change the content of the local file:

```bash
cat > ~/nginx-bindmount/index.html <<'EOF'
<!DOCTYPE html>
<html>
  <head>
    <title>Nginx Bind Mount</title>
  </head>
  <body>
    <h1>Hello from Bind Mount2 - Updated</h1>
  </body>
</html>
EOF
```

Verify the page again:

```bash
curl http://localhost:8080
```
![alt text](<../screenshots/Pasted image (24).png>)
The updated content should now appear.

## Step 6: Verify Logs Are Stored in the Volume
Check the logs directory inside the container:

```bash
docker exec nginx-lab7 ls -l /var/log/nginx
```

You can also inspect the volume data directly on the host:

```bash
sudo ls -R /var/lib/docker/volumes/nginx_logs/_data
```
![alt text](<../screenshots/Pasted image (25).png>)

And also the index file:

![alt text](<../screenshots/Pasted image (26).png>)
## Step 7: Clean Up
Stop and remove the container:

```bash
docker rm -f nginx-lab7
```

Delete the volume:

```bash
docker volume rm nginx_logs
```

## Notes
- A Docker volume is useful for persisting data such as logs.
- A bind mount links a directory from your host machine directly into the container.
- Changes made in the bind-mounted folder are reflected immediately in the container.

## Conclusion
This lab shows how to combine a persistent volume and a bind mount to manage both logs and web content for an Nginx container.
