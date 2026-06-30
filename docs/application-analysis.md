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


