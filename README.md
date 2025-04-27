# test-microserveice

An experimental project using microservice architecture.

## Overview

This project is a system composed of multiple microservices built using the Go language. It mainly consists of the following services:

- **Broker Service**: API that mediates requests to other services
- **Authentication Service**: Service responsible for user authentication
- **Frontend**: Web interface for users

## Technology Stack

- **Backend**: Go (Chi router)
- **Database**: PostgreSQL
- **Containerization**: Docker, Docker Compose
- **API**: RESTful API

## System Requirements

- Go 1.18 or higher
- Docker and Docker Compose
- Make (for build automation)

## Setup and Running Instructions

### Local Environment Setup

1. Clone the repository:
   ```
   git clone git@github.com:kento980037/test-microserveice.git
   cd test-microserveice
   ```

2. Start services using Docker Compose:
   ```
   cd project
   make up_build
   ```

   Note: Use `make up_build` for the first run. For subsequent starts, you can use `make up`.

This will containerize and start the backend services:
- Broker Service: http://localhost:8080
- Authentication Service: http://localhost:8081
- PostgreSQL Database: localhost:5433

### Starting the Frontend

The frontend runs directly on the local machine:
```
cd project
make build_front  # Build the frontend
make start        # Start the frontend
```

The frontend can be accessed at http://localhost:80.

### Containerizing and Running the Frontend

While the current project structure does not include Dockerfile settings for the frontend, you can containerize it using the following steps:

1. Create a Dockerfile for the frontend:
   ```
   # front-end/front-end.dockerfile
   FROM golang:1.18-alpine as builder
   WORKDIR /app
   COPY . .
   RUN CGO_ENABLED=0 go build -o frontApp ./cmd/web
   
   FROM alpine:latest
   WORKDIR /app
   COPY --from=builder /app/frontApp .
   COPY ./cmd/web/templates ./cmd/web/templates
   
   CMD ["./frontApp"]
   ```

2. Add the frontend service to docker-compose.yml:
   ```
   # Add to project/docker-compose.yml
   front-end:
     build:
       context: ./../front-end
       dockerfile: ./front-end.dockerfile
     restart: always
     ports:
       - "8082:80"
     deploy:
       mode: replicated
       replicas: 1
   ```

3. Add a container build command for the frontend to the Makefile:
   ```
   # Add to project/Makefile
   build_front_docker:
     @echo "Building front end docker image..."
     cd ../front-end && docker build -f front-end.dockerfile -t front-end .
     @echo "Done!"
   ```

### Stopping Services

To stop the services, run the following command:
```
cd project
make down
```

To stop the frontend:
```
cd project
make stop
```

## Project Structure

```
.
├── authentication-service/ # Authentication service
│   ├── cmd/               # Main application code
│   └── data/              # Database-related code
├── broker-service/        # Broker service
│   └── cmd/               # Main application code
├── front-end/             # Frontend application
│   └── cmd/               # Main application code
└── project/               # Project configuration files
    ├── Makefile           # Build and run automation scripts
    └── docker-compose.yml # Docker container configuration
```

## Development

Each service is designed to be developed and deployed independently. Communication between services is done via REST API.