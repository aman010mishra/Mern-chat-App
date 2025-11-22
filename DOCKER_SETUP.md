# Docker Setup Guide

This guide explains how to run the Chat Application using Docker and Docker Compose.

## Prerequisites

- Docker Desktop installed on your machine
- Docker Compose (comes with Docker Desktop)
- Git (for cloning the repository)

## Quick Start

### 1. Clone the Repository

```bash
git clone <your-repository-url>
cd <repository-name>
```

### 2. Configure Environment Variables

Create a `.env` file in the root directory by copying the example:

```bash
cp .env.example .env
```

Edit the `.env` file with your actual credentials:

```env
# MongoDB Configuration
MONGO_URI=mongodb://admin:adminpassword@mongo:27017/chatapp?authSource=admin
MONGO_USERNAME=admin
MONGO_PASSWORD=adminpassword

# Backend Configuration
PORT=8000
NODE_ENV=production

# JWT Secret
JWT_SECRET=your-super-secret-jwt-key

# Cloudinary Configuration
CLOUDINARY_CLOUD_NAME=your-cloudinary-cloud-name
CLOUDINARY_API_KEY=your-cloudinary-api-key
CLOUDINARY_API_SECRET=your-cloudinary-api-secret

# Docker Configuration
DOCKER_REGISTRY=
IMAGE_TAG=latest
```

### 3. Build and Run with Docker Compose

```bash
# Build all images
docker-compose build

# Start all services
docker-compose up -d

# View logs
docker-compose logs -f

# Check running containers
docker ps
```

### 4. Access the Application

- **Frontend**: http://localhost:80
- **Backend API**: http://localhost:8000
- **MongoDB**: localhost:27017

### 5. Stop the Application

```bash
# Stop all services
docker-compose down

# Stop and remove volumes (WARNING: This deletes all data)
docker-compose down -v
```

## Docker Commands Reference

### Building Images

```bash
# Build all services
docker-compose build

# Build specific service
docker-compose build backend
docker-compose build frontend

# Build without cache
docker-compose build --no-cache
```

### Running Containers

```bash
# Start all services in detached mode
docker-compose up -d

# Start specific service
docker-compose up -d backend

# Start with build
docker-compose up -d --build

# View logs
docker-compose logs -f
docker-compose logs -f backend
docker-compose logs -f frontend
```

### Managing Containers

```bash
# List running containers
docker-compose ps

# Stop all services
docker-compose stop

# Restart services
docker-compose restart

# Remove stopped containers
docker-compose down

# Remove containers and volumes
docker-compose down -v
```

### Debugging

```bash
# Execute command in running container
docker-compose exec backend sh
docker-compose exec frontend sh

# View logs
docker-compose logs backend
docker-compose logs frontend
docker-compose logs mongo

# Inspect container
docker inspect chatapp-backend
```

## Service Architecture

### Services

1. **MongoDB (mongo)**
   - Port: 27017
   - Volume: `mongo-data` for persistent storage
   - Health check enabled

2. **Backend**
   - Port: 8000
   - Depends on MongoDB
   - Node.js Express API
   - Socket.IO for real-time messaging

3. **Frontend**
   - Port: 80
   - Nginx web server
   - React application
   - Proxies API requests to backend

## Production Deployment

### Using Docker Hub

1. **Tag your images:**

```bash
docker tag chatapp-backend:latest yourusername/chatapp-backend:latest
docker tag chatapp-frontend:latest yourusername/chatapp-frontend:latest
```

2. **Push to Docker Hub:**

```bash
docker login
docker push yourusername/chatapp-backend:latest
docker push yourusername/chatapp-frontend:latest
```

3. **Pull and run on production server:**

```bash
docker pull yourusername/chatapp-backend:latest
docker pull yourusername/chatapp-frontend:latest
docker-compose up -d
```

## Troubleshooting

### Container won't start

```bash
# Check logs
docker-compose logs backend

# Rebuild without cache
docker-compose build --no-cache backend
docker-compose up -d backend
```

### Port already in use

```bash
# Find process using port
lsof -i :8000  # On macOS/Linux
netstat -ano | findstr :8000  # On Windows

# Change port in docker-compose.yml
ports:
  - "8001:8000"  # Map to different host port
```

### Database connection issues

```bash
# Check if MongoDB is running
docker-compose ps

# Check MongoDB logs
docker-compose logs mongo

# Verify environment variables
docker-compose exec backend env | grep MONGO
```

### Cannot connect to services

```bash
# Ensure all services are on the same network
docker network ls
docker network inspect chatapp_chatapp-network

# Restart all services
docker-compose restart
```

## Health Checks

All services include health checks:

- **MongoDB**: Pings database every 10 seconds
- **Backend**: HTTP check on `/api/auth/check` every 30 seconds
- **Frontend**: HTTP check on root path every 30 seconds

View health status:

```bash
docker-compose ps
```

## Performance Tips

1. **Use multi-stage builds** (already configured in Dockerfiles)
2. **Enable BuildKit** for faster builds:
   ```bash
   export DOCKER_BUILDKIT=1
   docker-compose build
   ```

3. **Use layer caching**:
   ```bash
   docker-compose build --build-arg BUILDKIT_INLINE_CACHE=1
   ```

4. **Limit resources** (add to docker-compose.yml):
   ```yaml
   services:
     backend:
       deploy:
         resources:
           limits:
             cpus: '0.5'
             memory: 512M
   ```

## Security Best Practices

1. ✅ Non-root users configured in containers
2. ✅ Multi-stage builds to reduce image size
3. ✅ Health checks enabled
4. ✅ Environment variables for secrets
5. ✅ Network isolation with custom bridge network

### Additional Security Recommendations

- Never commit `.env` file to git
- Use Docker secrets in production
- Regularly update base images
- Scan images for vulnerabilities:
  ```bash
  docker scan chatapp-backend:latest
  ```

## Cleanup

Remove all unused Docker resources:

```bash
# Remove stopped containers
docker container prune

# Remove unused images
docker image prune

# Remove unused volumes
docker volume prune

# Remove everything (WARNING: Destructive)
docker system prune -a --volumes
```
