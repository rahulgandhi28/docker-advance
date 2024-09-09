# Docker-Advance- Assignment

Optimized Dockerfile for Build Speed and Image Size


 # Original Docker File
   # Base image
FROM node:14

# Set working directory
WORKDIR /usr/src/app

# Install dependencies
COPY package*.json ./
RUN npm install

# Copy application code
COPY . .

# Expose port
EXPOSE 3000

# Start the application
CMD ["npm", "start"]


  


  

# Optimised Docker File 


# Stage 1: Build the application
FROM node:14 AS build-stage

# Set working directory
WORKDIR /usr/src/app

# Install dependencies
COPY package*.json ./
RUN npm install

# Copy application code
COPY . .

# Build the application (if needed, e.g., for TypeScript or build tools)
# RUN npm run build 

# Stage 2: Create the final image
FROM node:14-slim AS production-stage

# Set working directory
WORKDIR /usr/src/app

# Copy only the necessary artifacts from the build stage
COPY --from=build-stage /usr/src/app/node_modules ./node_modules
COPY --from=build-stage /usr/src/app/package*.json ./
COPY --from=build-stage /usr/src/app/dist ./dist

# Expose port
EXPOSE 3000

# Start the application
CMD ["node", "dist/server.js"]


# Setup a Docker Network from docker compose

version: '3.9'

services:
  app1:
    image: nginx
    networks:
      - my_network
    restart: unless-stopped

  app2:
    image: nginx
    networks:
      - my_network
    restart: unless-stopped

networks:
  my_network:
    driver: bridge



# To inspect the network 
docker network inspect my_network


# Create and Manage Volumes:
docker volume create my_volume
docker run -d --name my_container -v my_volume:/data myimage:latest

# Example with Multiple Containers
docker run -d --name test1 -v /host/data:/shared_data myimage
docker run -d --name test2 -v /host/data:/shared_data myimage


# Backup and Restore Data:
docker run --rm -v my_volume:/data -v $(pwd):/backup alpine tar czf /backup/backup.tar.gz -C /data .
docker run --rm -v my_volume:/data -v $(pwd):/backup alpine sh -c "tar xzf /backup/backup.tar.gz -C /data"

# Implementing Security Best Practices

  # User Permissions
# Add a non-root user in your Dockerfile:
RUN adduser --disabled-password myuser
USER myuser

# Image Vulnerability Scanning
docker scan myimage


# Secret Management:
echo "my_secret" | docker secret create my_secret -
docker service create --name my_service --secret my_secret myimage


# Run Containers with Least Privilege
docker run --user 1000:1000 mysql

# Use Docker Bench for Security
->Install Docker Bench for Security
git clone https://github.com/docker/docker-bench-security.git
cd docker-bench-security

-> Run docker bench 
sudo ./docker-bench-security.sh





