# Docker Registry Configuration Guide

## Problem
When attempting to login to the Docker registry at 192.168.0.98:8081, you encountered the following error:
```
Error response from daemon: Get "https://192.168.0.98:8081/v2/": http: server gave HTTP response to HTTPS client
```

This occurs because Docker is trying to use HTTPS to connect to your registry, but the registry is only serving HTTP content.

## Solution for macOS Docker Desktop

Since you're using Docker Desktop on macOS, follow these steps to configure your Docker installation to allow insecure registry connections:

1. Open Docker Desktop
2. Click on the Docker icon in the menu bar
3. Select "Preferences..." or "Settings..."
4. Go to "Docker Engine" tab
5. Add your registry to the "insecure-registries" list in the JSON configuration:

```json
{
  "insecure-registries": [
    "192.168.0.98:8081"
  ]
}
```

6. Click "Apply & Restart"
7. Wait for Docker to restart
8. Try logging in again with:
   ```
   docker login 192.168.0.98:8081
   ```

## Alternative: Configure Registry with HTTPS

A more secure approach would be to configure your registry with proper HTTPS certificates. This typically involves:

1. Obtaining SSL certificates for your registry domain
2. Configuring your registry to use these certificates
3. Ensuring your Docker client trusts these certificates

For development environments, self-signed certificates might be sufficient, but for production, proper CA-signed certificates are recommended.

## demo app - developing with Docker

This demo app shows a simple user profile app set up using 
- index.html with pure js and css styles
- nodejs backend with express module
- mongodb for data storage

All components are docker-based

### With Docker

#### To start the application

Step 1: Create docker network

    docker network create mongo-network 

Step 2: start mongodb 

    docker run -d -p 27017:27017 -e MONGO_INITDB_ROOT_USERNAME=admin -e MONGO_INITDB_ROOT_PASSWORD=password --name mongodb --net mongo-network mongo    

Step 3: start mongo-express
    
    docker run -d -p 8081:8081 -e ME_CONFIG_MONGODB_ADMINUSERNAME=admin -e ME_CONFIG_MONGODB_ADMINPASSWORD=password --net mongo-network --name mongo-express -e ME_CONFIG_MONGODB_SERVER=mongodb mongo-express   

_NOTE: creating docker-network in optional. You can start both containers in a default network. In this case, just emit `--net` flag in `docker run` command_

Step 4: open mongo-express from browser

    http://localhost:8081

Step 5: create `user-account` _db_ and `users` _collection_ in mongo-express

Step 6: Start your nodejs application locally - go to `app` directory of project 

    cd app
    npm install 
    node server.js
    
Step 7: Access you nodejs application UI from browser

    http://localhost:3000

### With Docker Compose

#### To start the application

Step 1: start mongodb and mongo-express

    docker-compose -f docker-compose.yaml up
    
_You can access the mongo-express under localhost:8080 from your browser_
    
Step 2: in mongo-express UI - create a new database "my-db"

Step 3: in mongo-express UI - create a new collection "users" in the database "my-db"       
    
Step 4: start node server 

    cd app
    npm install
    node server.js
    
Step 5: access the nodejs application from browser 

    http://localhost:3000

#### To build a docker image from the application

    docker build -t my-app:1.0 .       
    
The dot "." at the end of the command denotes location of the Dockerfile.
