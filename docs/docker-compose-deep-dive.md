# Docker Compose Deep Dive

## Overview

Docker Compose is a tool that simplifies the deployment and management of multi-container Docker applications using a single YAML configuration file.

Instead of creating and connecting containers manually, Docker Compose automates the entire process including:

- Building Docker images
- Creating containers
- Creating Docker networks
- Starting services
- Managing dependencies
- Exposing ports
- Performing health checks

In the Robot Shop project, Docker Compose is responsible for deploying all application services, databases, caching services, and message brokers with a single command.

---

# Docker Compose File Structure

The Robot Shop project uses a single `docker-compose.yaml` file.

The compose file consists of three major sections:

```
docker-compose.yaml

├── version
├── services
└── networks
```

Each section has a specific responsibility.

---

# Version

```yaml
version: "3"
```

The version specifies which Compose file format is being used.

Although Docker Compose v2 ignores this field, it is still commonly found in many existing projects for backward compatibility.

---

# Services

The `services` section defines every container that belongs to the application.

Robot Shop contains the following services:

| Service | Technology | Purpose |
|----------|------------|---------|
| web | Nginx | Frontend |
| catalogue | Node.js | Product Catalogue |
| cart | Node.js | Shopping Cart |
| user | Node.js | User Management |
| payment | Python | Payment Processing |
| shipping | Java | Shipping |
| ratings | PHP | Product Ratings |
| dispatch | Go | Order Dispatch |
| mongodb | MongoDB | Product Database |
| mysql | MySQL | Shipping Database |
| redis | Redis | Cache |
| rabbitmq | RabbitMQ | Message Broker |

Each service runs inside its own isolated Docker container.

---

# Build Configuration

Application services use Dockerfiles to build custom images.

Example:

```yaml
catalogue:
  build:
    context: catalogue
```

The `context` specifies the directory that contains the Dockerfile.

Docker automatically builds the image before starting the container.

---

# Image Configuration

Each service specifies which Docker image should be used.

Example:

```yaml
image: ${REPO}/rs-catalogue:${TAG}
```

Instead of hardcoding the repository and version, Docker Compose loads them from the `.env` file.

Example:

```
REPO=faraz227
TAG=v1
```

This makes version management much easier.

---

# Environment Variables

Several services require environment variables.

Example:

```yaml
environment:
    APP_ENV: prod
```

Environment variables allow applications to be configured without changing the source code.

They are commonly used for:

- Database hosts
- API endpoints
- Application modes
- Credentials
- Feature flags

---

# Depends On

Some services require other services to be available before they start.

Example:

```yaml
depends_on:
    - mongodb
```

This ensures that MongoDB starts before the Catalogue service.

Examples from this project:

Catalogue → MongoDB

Cart → Redis

Shipping → MySQL

Payment → RabbitMQ

User → MongoDB + Redis

Although `depends_on` controls startup order, it does not guarantee that the dependent service is fully ready.

For this reason, health checks are also used.

---

# Health Checks

Docker Compose continuously monitors the health of important services.

Example:

```yaml
healthcheck:
    test:
```

A health check periodically sends requests to the application.

Possible container states are:

- starting
- healthy
- unhealthy

When running:

```bash
docker compose ps
```

Healthy services appear as:

```
healthy
```

This makes troubleshooting much easier.

---

# Port Mapping

The Web service exposes the application to users.

```yaml
ports:
  - "8080:8080"
```

Meaning:

Host

```
localhost:8080
```

↓

Container

```
8080
```

The remaining backend services communicate internally and therefore do not expose ports to the host machine.

---

# Logging

Every service uses JSON logging.

```yaml
logging:
    driver: json-file
```

The compose file also limits log size.

```yaml
max-size: 25m
max-file: 2
```

Benefits:

- Prevents unlimited log growth
- Saves disk space
- Simplifies log management

---

# Networks

All services are attached to a custom Docker bridge network.

```yaml
robot-shop
```

This network allows every container to communicate securely using service names.

Example:

```
catalogue → mongodb

cart → redis

shipping → mysql

payment → rabbitmq
```

Docker automatically provides internal DNS resolution.

---

# Application Startup Flow

The deployment sequence is approximately:

1. Docker Compose creates the custom network.
2. MongoDB starts.
3. MySQL starts.
4. Redis starts.
5. RabbitMQ starts.
6. Backend microservices start.
7. Web service starts.
8. Health checks begin.
9. The application becomes available on localhost:8080.

---

# Commands Used

Start all services

```bash
docker compose up -d
```

Stop all services

```bash
docker compose down
```

View running containers

```bash
docker compose ps
```

View logs

```bash
docker compose logs
```

---

# Key Learnings

- Docker Compose manages multi-container applications.
- One YAML file defines the complete infrastructure.
- Environment variables simplify configuration.
- Health checks monitor application availability.
- Depends_on controls startup sequence.
- Docker Compose automatically creates Docker networks.
- Internal communication uses Docker DNS instead of IP addresses.