# E-Commerce Microservices Application

A comprehensive microservices-based e-commerce application built with Spring Boot, demonstrating modern distributed systems architecture patterns.

## 📋 Project Overview

This project implements a full-featured e-commerce platform using microservices architecture. It showcases best practices for building scalable, resilient, and maintainable distributed systems with Spring Boot and Spring Cloud.

## 🏗️ Architecture

The application consists of 7 microservices:

- **user-service** - User management and authentication
- **product-service** - Product catalog management
- **order-service** - Order processing and cart management
- **notification-service** - Event-driven notifications
- **gateway** - API Gateway with routing and security
- **eureka** - Service discovery server
- **configserver** - Centralized configuration management

## ✨ Features

### User Service
- User registration and profile management
- Address management
- Keycloak integration for authentication
- MongoDB for data persistence

### Product Service
- Product CRUD operations
- Product catalog management
- PostgreSQL for data persistence
- Search and filtering capabilities

### Order Service
- Order creation and management
- Shopping cart functionality
- Order status tracking
- Integration with User and Product services
- Kafka event publishing for notifications
- Circuit breaker pattern for fault tolerance

### Notification Service
- Event-driven architecture using Kafka
- Order event consumption
- Email notification processing
- Asynchronous message handling

### API Gateway
- Single entry point for all services
- OAuth2 resource server security
- Redis-based caching
- Request routing and load balancing
- Rate limiting and circuit breaking

### Infrastructure Services
- **Eureka Server** - Service discovery and registration
- **Config Server** - Centralized configuration with Spring Cloud Config
- **Spring Cloud Bus** - Configuration refresh with RabbitMQ

## 🛠️ Technology Stack

### Core Technologies
- **Java 21** - Modern Java with virtual threads
- **Spring Boot 3.4.x** - Latest Spring Boot framework
- **Spring Cloud 2024.0.x** - Microservices patterns
- **Maven** - Build and dependency management

### Databases
- **MongoDB** - User service data store
- **PostgreSQL** - Product and Order service data store
- **Redis** - Gateway caching layer

### Messaging & Communication
- **Apache Kafka** - Event streaming (Order → Notification)
- **RabbitMQ** - Spring Cloud Bus for config refresh
- **Spring Cloud Stream** - Message binding abstraction

### Service Infrastructure
- **Netflix Eureka** - Service discovery
- **Spring Cloud Config** - Centralized configuration
- **Spring Cloud Gateway** - API gateway
- **Resilience4j** - Circuit breaker and retry
- **Spring Cloud OpenFeign** - Declarative REST clients

### Monitoring & Observability
- **Spring Boot Actuator** - Health checks and metrics
- **Prometheus** - Metrics collection
- **Zipkin** - Distributed tracing
- **Micrometer** - Metrics registry

### Security
- **OAuth2 Resource Server** - Token-based authentication
- **Keycloak** - Identity and access management

### Containerization
- **Docker** - Container runtime
- **Jib Maven Plugin** - Container image building
- **Distroless Java 21** - Minimal base images

## 📦 Services & Ports

| Service | Port | Description |
|---------|------|-------------|
| configserver | 8888 | Configuration server |
| eureka | 8761 | Service discovery |
| gateway | 8080 | API gateway |
| user-service | 8081 | User management |
| product-service | 8082 | Product catalog |
| order-service | 8083 | Order processing |
| notification-service | 8084 | Notifications |

## 🚀 Getting Started

For detailed instructions on how to set up and run the application, please refer to [HOW_TO_USE.md](HOW_TO_USE.md)

## 📖 Documentation

- [HOW_TO_USE.md](HOW_TO_USE.md) - Complete setup and execution guide
- [API Documentation](#) - API endpoints reference (coming soon)

## 👤 Author

**Pratik** - Developer and Maintainer

## 📄 License

This project is for educational purposes.
