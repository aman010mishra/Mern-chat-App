# Stage 1: Build the frontend
FROM node:20 AS build

WORKDIR /app

# Install frontend dependencies
COPY package*.json ./
RUN npm install

# Copy frontend source code
COPY . .

# Build the frontend
RUN npm run build

# Stage 2: Serve the frontend
FROM nginx:alpine

# Copy the built frontend files to the Nginx container
COPY --from=build /app/dist /usr/share/nginx/html

# Copy custom nginx configuration for SPA routing
COPY nginx.conf /etc/nginx/conf.d/default.conf

# Expose port 80 for the frontend
EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]