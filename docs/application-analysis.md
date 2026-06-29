# Application Analysis

## Frontend

- Folder: web/
- Technology: Nginx, HTML, CSS, JavaScript
- Purpose: Provides web interface to users

## Backend Services

### Cart Service
- Folder: cart/
- Technology: Node.js

### Catalogue Service
- Folder: catalogue/
- Technology: Node.js

### User Service
- Folder: user/
- Technology: Node.js

### Shipping Service
- Folder: shipping/
- Technology: Java

### Payment Service
- Folder: payment/
- Technology: Python

### Ratings Service
- Folder: ratings/
- Technology: PHP

## Databases

### MongoDB
- Folder: mongo/
- Purpose: Stores product and user data

### MySQL
- Folder: mysql/
- Purpose: Stores ratings and shipping data

### Redis
- Purpose: Caching

### RabbitMQ
- Purpose: Messaging between microservices

## Application Flow

User → Web → Backend Microservices → Databases



----------------------------------------------------------------




# Application Analysis

## Project Overview

The Robot Shop application is a cloud-native microservices-based e-commerce platform designed to demonstrate modern DevOps practices and microservices architecture. The application consists of multiple loosely coupled services communicating over a Docker network.

---

## Frontend Layer

### Web Service

* **Folder:** `web/`
* **Technology:** Nginx, HTML, CSS, JavaScript
* **Port:** 8080
* **Purpose:**

  * Serves static web content to users.
  * Acts as the presentation layer.
  * Routes user requests to backend microservices.

---

## Backend Microservices

### Cart Service

* **Folder:** `cart/`
* **Technology:** Node.js
* **Port:** 8080
* **Purpose:** Manages shopping cart operations.

### Catalogue Service

* **Folder:** `catalogue/`
* **Technology:** Node.js
* **Port:** 8080
* **Purpose:** Provides product catalogue information.

### User Service

* **Folder:** `user/`
* **Technology:** Node.js
* **Port:** 8080
* **Purpose:** Handles user registration, authentication, and profile management.

### Shipping Service

* **Folder:** `shipping/`
* **Technology:** Java (Spring Boot)
* **Port:** 8080
* **Purpose:** Calculates shipping costs and manages shipping information.

### Payment Service

* **Folder:** `payment/`
* **Technology:** Python
* **Port:** 8080
* **Purpose:** Processes payment transactions.

### Ratings Service

* **Folder:** `ratings/`
* **Technology:** PHP
* **Port:** 80
* **Purpose:** Manages product ratings and reviews.

### Dispatch Service

* **Folder:** `dispatch/`
* **Technology:** Go
* **Purpose:** Handles asynchronous order dispatch processing.

---

## Database and Messaging Layer

### MongoDB

* **Folder:** `mongo/`
* **Port:** 27017
* **Purpose:**

  * Stores product catalogue data.
  * Stores user information.

### MySQL

* **Folder:** `mysql/`
* **Port:** 3306
* **Purpose:**

  * Stores shipping data.
  * Stores ratings data.

### Redis

* **Port:** 6379
* **Purpose:**

  * Provides in-memory caching.
  * Stores shopping cart and session-related data.

### RabbitMQ

* **Port:** 5672
* **Purpose:**

  * Acts as a message broker.
  * Enables asynchronous communication between services.

---

## Application Workflow

1. The user accesses the application through the Web UI.
2. Nginx serves static content and routes requests to backend microservices.
3. Backend services process business logic.
4. Services communicate with databases and message queues as required.
5. The processed response is returned to the user.

---

## High-Level Flow

```text
User → Web UI (Nginx) → Microservices → Databases / Message Broker
```


-------------------------------------------------------------------


# Docker Image Analysis

## Overview

The Robot Shop application follows a microservices architecture where each service is packaged as an independent Docker container. Each microservice uses a technology-specific base image to provide an optimized runtime environment.

---

# Web Service Dockerfile Analysis

## Dockerfile

```Dockerfile
FROM nginx:1.21.6

EXPOSE 8080

ENV CATALOGUE_HOST=catalogue \
    USER_HOST=user \
    CART_HOST=cart \
    SHIPPING_HOST=shipping \
    PAYMENT_HOST=payment \
    RATINGS_HOST=ratings

COPY entrypoint.sh /root/
ENTRYPOINT ["/root/entrypoint.sh"]

COPY default.conf.template /etc/nginx/conf.d/default.conf.template
COPY static /usr/share/nginx/html
```

## Analysis

### Base Image

* **Image:** `nginx:1.21.6`
* **Purpose:** Provides a lightweight and production-ready web server.

### Port

* Exposes port `8080` to serve web traffic.

### Environment Variables

Environment variables define the backend service endpoints:

* Catalogue → `catalogue`
* User → `user`
* Cart → `cart`
* Shipping → `shipping`
* Payment → `payment`
* Ratings → `ratings`

Docker internal DNS resolves these service names automatically.

### ENTRYPOINT

The container starts by executing:

```bash
/root/entrypoint.sh
```

The script dynamically generates the Nginx configuration before starting Nginx.

### Static Content

Frontend files are copied into:

```text
/usr/share/nginx/html
```

---

# Cart Service Dockerfile Analysis

## Dockerfile

```Dockerfile
FROM node:14

ENV INSTANA_AUTO_PROFILE true

EXPOSE 8080

WORKDIR /opt/server

COPY package.json /opt/server/

RUN npm install

COPY server.js /opt/server/

CMD ["node", "server.js"]
```

## Analysis

### Base Image

* **Image:** `node:14`
* **Reason:** Node.js runtime is required to execute JavaScript applications.

### Working Directory

Application files are stored inside:

```text
/opt/server
```

### Dependency Installation

```bash
npm install
```

installs all application dependencies from `package.json`.

### Container Startup

Application starts using:

```bash
node server.js
```

---

# Catalogue Service Dockerfile Analysis

## Base Image

* **Image:** `node:14`

## Runtime

Runs a Node.js application.

## Startup Command

```bash
node server.js
```

## Exposed Port

```text
8080
```

---

# User Service Dockerfile Analysis

## Base Image

* **Image:** `node:14`

## Purpose

Handles authentication and user management.

## Startup Command

```bash
node server.js
```

---

# Payment Service Dockerfile Analysis

## Dockerfile

```Dockerfile
FROM python:3.9

EXPOSE 8080

WORKDIR /app

COPY requirements.txt /app/

RUN pip install -r requirements.txt

COPY *.py /app/
COPY payment.ini /app/

CMD ["uwsgi", "--ini", "payment.ini"]
```

## Analysis

### Base Image

* **Image:** `python:3.9`
* **Reason:** Python runtime environment.

### Dependency Installation

Dependencies are installed from:

```text
requirements.txt
```

using:

```bash
pip install -r requirements.txt
```

### Startup Method

Application runs using:

```bash
uwsgi --ini payment.ini
```

uWSGI is a production-grade application server used for Python applications.

---

# Shipping Service Dockerfile Analysis

## Multi-Stage Build

The Shipping service uses a multi-stage Docker build.

---

## Stage 1: Build Stage

```Dockerfile
FROM debian:10 AS build
```

### Purpose

Compiles the Java application.

### Installed Tools

* Maven
* Java certificates

### Build Process

```bash
mvn dependency:resolve
mvn package
```

creates:

```text
shipping-1.0.jar
```

---

## Stage 2: Runtime Stage

```Dockerfile
FROM openjdk:8-jdk
```

### Purpose

Provides a lightweight Java runtime environment.

### Environment Variables

```text
CART_ENDPOINT=cart:8080
DB_HOST=mysql
```

These variables define internal service communication.

### Startup Command

```bash
java -Xmn256m -Xmx768m -jar shipping.jar
```

starts the Java application.

---

# Key Learnings

* Each microservice uses a technology-specific base image.
* Docker images encapsulate application code and dependencies.
* Containers expose only required ports.
* Docker Compose networking allows service-to-service communication using service names.
* Multi-stage builds reduce image size and improve security.
