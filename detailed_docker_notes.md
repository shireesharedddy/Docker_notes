
# Detailed Docker Notes

## 1. Introduction to Docker
Docker is a platform that helps developers develop, ship, and run applications inside containers. Containers encapsulate an application with all its dependencies, providing a consistent environment across different systems.

## 2. Key Concepts
### a. Docker Image
An image is a package that contains everything needed to run an application: code, runtime, libraries, and environment variables. Images are immutable.

### b. Docker Container
A container is a running instance of a Docker image. It isolates the application and provides consistency, ensuring it runs the same across different environments.

### c. Dockerfile
A Dockerfile is a script containing instructions for building a Docker image.

#### Example Dockerfile Syntax:
```Dockerfile
# Base image
FROM node:14

# Set the working directory
WORKDIR /app

# Copy package.json and install dependencies
COPY package*.json ./
RUN npm install

# Copy the rest of the application code
COPY . .

# Expose the application port
EXPOSE 3000

# Command to run the application
CMD ["npm", "start"]
```

### d. Docker Volumes
Volumes allow you to store data outside the container, ensuring persistence when the container stops.

#### Volume Commands:
- `docker volume create <volume_name>`: Creates a volume.
- `docker run -v <volume_name>:/path/in/container <image>`: Mounts a volume in the container.

### e. Docker Networks
Docker allows containers to communicate with each other using networks.

#### Network Commands:
- `docker network create <network_name>`: Creates a custom network.
- `docker run --network <network_name> <image>`: Runs a container in a custom network.

## 3. Basic Docker Commands
- `docker pull <image>`: Pulls an image from Docker Hub.
- `docker run <image>`: Runs a container from the image.
- `docker ps`: Lists running containers.
- `docker stop <container_id>`: Stops a running container.
- `docker rm <container_id>`: Removes a stopped container.
- `docker rmi <image_id>`: Removes an image from the local machine.
- `docker build -t <image_name> .`: Builds a Docker image from a Dockerfile.

## 4. Docker Compose
Docker Compose is a tool to define multi-container applications. You define services, networks, and volumes in a `docker-compose.yml` file.

#### Example `docker-compose.yml`:
```yaml
version: '3.8'
services:
  app:
    image: myapp
    ports:
      - "8080:80"
  db:
    image: postgres
    environment:
      POSTGRES_DB: mydb
      POSTGRES_USER: user
      POSTGRES_PASSWORD: password
```

## 5. Advanced Topics in Docker Compose
### a. Service Dependencies
The `depends_on` option ensures that services start in a specific order.

#### Example:
```yaml
services:
  web:
    image: nginx
    depends_on:
      - db
  db:
    image: postgres
```

### b. Health Checks
Health checks ensure that services are running correctly before others start.

#### Example:
```yaml
services:
  app:
    image: myapp
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8080/health"]
      interval: 30s
      timeout: 10s
      retries: 3
```

### c. Multi-Environment Configuration
You can define multiple configurations for different environments (e.g., development and production).

#### Command:
```
docker-compose -f docker-compose.yml -f docker-compose.prod.yml up
```

### d. Data Persistence
Docker Compose allows the definition of volumes for persistent data storage.

#### Example:
```yaml
volumes:
  db-data:
services:
  db:
    volumes:
      - db-data:/var/lib/postgresql/data
```

## 6. Networking in Docker Compose
Custom networks allow inter-service communication.

#### Example:
```yaml
networks:
  my-network:
services:
  app:
    networks:
      - my-network
  db:
    networks:
      - my-network
```

## 7. Best Practices for Docker and Docker Compose
1. Keep Dockerfiles simple and modular.
2. Use `.env` files to manage environment variables.
3. Regularly update images for security patches.
4. Use named volumes for better data management.
5. Document complex Docker Compose configurations.

## 8. Building a Sample Application with Docker Compose

### Directory Structure:
```
my-app/
├── backend/
│   ├── app.js
│   ├── Dockerfile
│   └── package.json
├── frontend/
│   └── Dockerfile
├── docker-compose.yml
```

### Backend Dockerfile:
```Dockerfile
FROM node:14
WORKDIR /usr/src/app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3000
CMD ["npm", "start"]
```

### Frontend Dockerfile:
```Dockerfile
FROM nginx:alpine
COPY ./html /usr/share/nginx/html
```

### Docker Compose File:
```yaml
version: '3.8'
services:
  web:
    build:
      context: ./frontend
    ports:
      - "8080:80"
  db:
    image: postgres
    environment:
      POSTGRES_DB: mydb
      POSTGRES_USER: user
      POSTGRES_PASSWORD: password
    volumes:
      - db-data:/var/lib/postgresql/data
  backend:
    build:
      context: ./backend
    depends_on:
      - db
volumes:
  db-data:
```

### Running the Application:
- `docker-compose up --build`
- Access frontend at `http://localhost:8080`
- Access backend at `http://localhost:3000`

## Conclusion
Docker and Docker Compose provide powerful tools for managing containers and multi-container applications, ensuring a smooth and consistent deployment process.
