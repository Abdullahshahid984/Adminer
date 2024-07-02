# Project Portfolio

## Project Overview
This project is a full-stack web application designed with a focus on modularity and scalability. The architecture is composed of multiple services, including a PostgreSQL database, an admin interface (Adminer), a Node.js backend for authentication, and a frontend served by Nginx. The project utilizes Docker for containerization and orchestration, ensuring a seamless development and deployment process.

## Technologies Used

### Frontend
- **React.js**: The frontend is built with React, providing a dynamic and responsive user interface.
- **Nginx**: Used as a web server to serve the built React application.

### Backend
- **Node.js**: The backend is developed using Node.js, offering a robust environment for building scalable network applications.
- **Express.js**: Express framework is used for handling server-side logic and APIs.
- **PostgreSQL**: A powerful, open-source relational database system used to store and manage data.
- **Adminer**: A database management tool to manage PostgreSQL databases easily.

### DevOps Tools
- **Docker**: Used for containerizing the application, ensuring consistency across various environments.
- **Docker Compose**: Used for orchestrating multi-container Docker applications.

## Project Structure

### Docker Configuration

#### Frontend (`web/Dockerfile`)
```Dockerfile
# Stage 1: Build
FROM node:16 as build
WORKDIR /usr/src/app
COPY web/package*.json ./
RUN npm install
COPY web/ ./
RUN npm run build

# Stage 2: Serve
FROM nginx:alpine
COPY --from=build /usr/src/app/build /usr/share/nginx/html
COPY web/nginx.conf /etc/nginx/conf.d/default.conf
```

#### Backend (`backend/auth/Dockerfile`)
```Dockerfile
# Use the official Node.js image
FROM node:16

# Set working directory
WORKDIR /usr/src/app

# Set npm registry to Yarn's registry
RUN npm set registry https://registry.yarnpkg.com

# Copy the package.json and package-lock.json
COPY backend/auth/package*.json ./

# Install dependencies
RUN npm install

# Copy the rest of the application code into the container
COPY backend/auth/ .

# Expose the port the app runs on
EXPOSE 3000

# Command to run the application
CMD ["npm", "start"]
```

### Docker Compose Configuration
```yaml
version: '3.8'

services:
  postgresdb:
    image: postgres:latest
    environment:
      POSTGRES_USER: ${DB_USERNAME}
      POSTGRES_PASSWORD: ${DB_PASSWORD}
      POSTGRES_DB: ${AUTH_DB}
    ports:
      - "${POSTGRES_PORT}:5432"
  adminer:
    image: adminer:latest
    ports:
      - "8080:8080"
  backend:
    build:
      context: .
      dockerfile: backend/auth/Dockerfile
    environment:
      DB_USERNAME: ${DB_USERNAME}
      DB_PASSWORD: ${DB_PASSWORD}
      DB_HOST: ${DB_HOST}
      AUTH_DB: ${AUTH_DB}
    ports:
      - "3000:3000"
    depends_on:
      - postgresdb
  web:
    build:
      context: .
      dockerfile: web/Dockerfile
    ports:
      - "80:80"
```

### Environment Variables
The following environment variables are used to configure the services:
- **DB_USERNAME**: The username for the PostgreSQL database.
- **DB_PASSWORD**: The password for the PostgreSQL database.
- **AUTH_DB**: The name of the PostgreSQL database.
- **POSTGRES_PORT**: The port on which the PostgreSQL database runs.

## Development Workflow

1. **Building the Containers**: The project is divided into frontend and backend services, each with its own Dockerfile. The frontend is built using a multi-stage build to optimize the final image size and served using Nginx. The backend is built using Node.js and connects to a PostgreSQL database.

2. **Running the Services**: Docker Compose is used to manage and run all services. This includes the PostgreSQL database, the Adminer interface, the backend server, and the frontend application.

3. **Database Management**: Adminer is included as a service to provide a web-based interface for managing the PostgreSQL database, making it easier to perform database operations during development and testing.

4. **Development and Testing**: The environment variables ensure that each service is properly configured. During development, changes to the code are automatically reflected in the running containers, providing a seamless development experience.

## Conclusion
This project showcases a modern web application architecture using Docker for containerization and orchestration. By separating the frontend, backend, and database services, the application is both modular and scalable, making it easier to maintain and extend. Docker Compose simplifies the management of these services, ensuring a consistent environment across development, testing, and production.
