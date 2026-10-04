# How to Use E-Commerce Microservices Application

This guide provides step-by-step instructions for setting up, running, and understanding the execution flow of the e-commerce microservices application.

## 📋 Prerequisites

Before running the application, ensure you have the following installed:

- **Java 21** - [Download JDK 21](https://www.oracle.com/java/technologies/downloads/#java21)
- **Maven 3.8+** - [Download Maven](https://maven.apache.org/download.cgi)
- **MongoDB** - [Download MongoDB](https://www.mongodb.com/try/download/community)
- **PostgreSQL 15+** - [Download PostgreSQL](https://www.postgresql.org/download/)
- **Redis** - [Download Redis](https://redis.io/download)
- **Apache Kafka** - [Download Kafka](https://kafka.apache.org/downloads)
- **RabbitMQ** - [Download RabbitMQ](https://www.rabbitmq.com/download.html)
- **Docker** (optional, for containerization) - [Download Docker](https://www.docker.com/products/docker-desktop)

## 🚀 Quick Start with Docker

The easiest way to run all infrastructure services is using Docker Compose.

### Step 1: Start Infrastructure Services

```bash
# Start MongoDB, PostgreSQL, Redis, Kafka, RabbitMQ
docker-compose up -d
```

### Step 2: Build All Services

```bash
# Build all microservices
mvn clean install -DskipTests
```

### Step 3: Start Services in Order

Start the services in the following order:

1. **Config Server** (Port 8888)
```bash
cd configserver
mvn spring-boot:run
```

2. **Eureka Server** (Port 8761)
```bash
cd eureka
mvn spring-boot:run
```

3. **Gateway** (Port 8080)
```bash
cd gateway
mvn spring-boot:run
```

4. **User Service** (Port 8081)
```bash
cd user
mvn spring-boot:run
```

5. **Product Service** (Port 8082)
```bash
cd product
mvn spring-boot:run
```

6. **Order Service** (Port 8083)
```bash
cd order
mvn spring-boot:run
```

7. **Notification Service** (Port 8084)
```bash
cd notification
mvn spring-boot:run
```

## 🔄 Execution Flow

### Application Startup Sequence

1. **Infrastructure Services**
   - MongoDB starts on port 27017
   - PostgreSQL starts on port 5432
   - Redis starts on port 6379
   - Kafka starts on port 9092
   - RabbitMQ starts on port 5672

2. **Config Server** (Port 8888)
   - Loads configuration files from `configserver/src/main/resources/config`
   - Provides centralized configuration to all services
   - Connects to RabbitMQ for config refresh events

3. **Eureka Server** (Port 8761)
   - Starts service discovery registry
   - All microservices register themselves with Eureka
   - Enables service-to-service communication via service names

4. **API Gateway** (Port 8080)
   - Registers with Eureka
   - Loads configuration from Config Server
   - Sets up routing rules to all services
   - Configures OAuth2 security
   - Connects to Redis for caching

5. **User Service** (Port 8081)
   - Registers with Eureka
   - Loads configuration from Config Server
   - Connects to MongoDB
   - Exposes REST APIs for user management

6. **Product Service** (Port 8082)
   - Registers with Eureka
   - Loads configuration from Config Server
   - Connects to PostgreSQL
   - Exposes REST APIs for product management

7. **Order Service** (Port 8083)
   - Registers with Eureka
   - Loads configuration from Config Server
   - Connects to PostgreSQL
   - Connects to Kafka for event publishing
   - Uses OpenFeign to communicate with User and Product services
   - Implements circuit breaker with Resilience4j

8. **Notification Service** (Port 8084)
   - Registers with Eureka
   - Connects to Kafka for event consumption
   - Listens for order events
   - Processes notifications asynchronously

### Typical Request Flow

#### 1. User Registration Flow

```
Client → Gateway → User Service → MongoDB
```

1. Client sends registration request to Gateway
2. Gateway routes to User Service
3. User Service saves user to MongoDB
4. User Service integrates with Keycloak for authentication
5. Response sent back through Gateway

#### 2. Product Browsing Flow

```
Client → Gateway → Product Service → PostgreSQL
```

1. Client requests products from Gateway
2. Gateway routes to Product Service (with Redis caching)
3. Product Service queries PostgreSQL
4. Response cached in Redis for subsequent requests
5. Response sent back through Gateway

#### 3. Order Creation Flow

```
Client → Gateway → Order Service → (User Service + Product Service) → Kafka → Notification Service
```

1. Client sends order request to Gateway
2. Gateway routes to Order Service
3. Order Service validates user via User Service (using OpenFeign)
4. Order Service validates products via Product Service (using OpenFeign)
5. Order Service saves order to PostgreSQL
6. Order Service publishes `OrderCreatedEvent` to Kafka
7. Notification Service consumes event from Kafka
8. Notification Service processes and sends notification
9. Response sent back through Gateway

## 🧪 Testing the Application

### Health Checks

Check if all services are running:

```bash
# Eureka Dashboard
http://localhost:8761

# Gateway Health
http://localhost:8080/actuator/health

# User Service Health
http://localhost:8081/actuator/health

# Product Service Health
http://localhost:8082/actuator/health

# Order Service Health
http://localhost:8083/actuator/health
```

### API Endpoints

#### User Service (via Gateway)
```
POST   /api/users/register        - Register new user
GET    /api/users/{id}            - Get user by ID
PUT    /api/users/{id}            - Update user
DELETE /api/users/{id}            - Delete user
POST   /api/users/{id}/address    - Add address
```

#### Product Service (via Gateway)
```
POST   /api/products              - Create product
GET    /api/products              - Get all products
GET    /api/products/{id}        - Get product by ID
PUT    /api/products/{id}        - Update product
DELETE /api/products/{id}        - Delete product
```

#### Order Service (via Gateway)
```
POST   /api/orders                - Create order
GET    /api/orders/{id}          - Get order by ID
GET    /api/orders/user/{userId} - Get orders by user
POST   /api/cart                 - Add to cart
GET    /api/cart/{userId}       - Get user cart
DELETE /api/cart/{itemId}        - Remove from cart
```

## 🔧 Configuration

### Database Configuration

#### MongoDB (User Service)
```yaml
spring:
  data:
    mongodb:
      uri: mongodb://localhost:27017/userdb
```

#### PostgreSQL (Product & Order Service)
```yaml
spring:
  datasource:
    url: jdbc:postgresql://localhost:5432/productdb
    username: postgres
    password: postgres
  jpa:
    hibernate:
      ddl-auto: update
```

### Service Discovery

All services register with Eureka at:
```yaml
eureka:
  client:
    service-url:
      defaultZone: http://localhost:8761/eureka
```

### Configuration Server

Services load configuration from:
```yaml
spring:
  config:
    import: optional:configserver:http://localhost:8888
```

## 🐛 Troubleshooting

### Services Not Starting

1. **Check if Config Server is running**: All services depend on Config Server
2. **Check if Eureka is running**: Services need to register with Eureka
3. **Verify database connections**: Ensure MongoDB, PostgreSQL are running
4. **Check port conflicts**: Ensure no other applications are using the same ports

### Services Not Registering with Eureka

1. Check Eureka server is accessible at `http://localhost:8761`
2. Verify service configuration has correct Eureka URL
3. Check service logs for registration errors

### Kafka Connection Issues

1. Ensure Kafka is running on port 9092
2. Check Kafka topics are created
3. Verify Kafka configuration in Order and Notification services

### Configuration Not Loading

1. Check Config Server is running on port 8888
2. Verify configuration files exist in Config Server's config directory
3. Check service's bootstrap configuration

## 📊 Monitoring

### Actuator Endpoints

All services expose Actuator endpoints:

- `/actuator/health` - Health status
- `/actuator/metrics` - Application metrics
- `/actuator/prometheus` - Prometheus metrics
- `/actuator/info` - Application information

### Distributed Tracing

Zipkin is configured for distributed tracing:

- Zipkin Server: `http://localhost:9411`
- View traces at Zipkin UI

### Metrics with Prometheus

Prometheus metrics are exposed at `/actuator/prometheus` endpoint.

## 🐳 Docker Deployment

### Build Docker Images

```bash
# Build all service images
mvn clean package jib:dockerBuild
```

### Run with Docker Compose

```bash
# Start all services
docker-compose up -d

# View logs
docker-compose logs -f

# Stop all services
docker-compose down
```

## 📝 Notes

- Services use service discovery (Eureka) for inter-service communication
- Gateway is the single entry point for all external requests
- Configuration is centralized in Config Server
- Events are propagated via Kafka for asynchronous processing
- Circuit breakers prevent cascading failures
- All services expose health checks via Actuator
