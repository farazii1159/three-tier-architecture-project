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