# API Gateway Pattern in Doku

## Overview

This document explains how to implement an API Gateway pattern similar to enterprise microservices architectures.

## Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                         Client/Browser                       │
└────────────────────────┬────────────────────────────────────┘
                         │ HTTPS
                         ↓
┌─────────────────────────────────────────────────────────────┐
│                    Traefik (Reverse Proxy)                   │
│                     https://api.doku.local                   │
└────────────────────────┬────────────────────────────────────┘
                         │
                         ↓
┌─────────────────────────────────────────────────────────────┐
│                     API Gateway Container                     │
│              (Spring Cloud Gateway / Kong / etc)             │
│                                                              │
│  ┌─────────────────────────────────────────────────────┐   │
│  │  • Token Validation                                  │   │
│  │  • Authorization                                     │   │
│  │  • Rate Limiting                                     │   │
│  │  │  • Request Routing                                    │   │
│  └─────────────────────────────────────────────────────┘   │
└────────────┬──────────────┬──────────────┬─────────────────┘
             │              │              │
             ↓              ↓              ↓
   ┌─────────────┐  ┌─────────────┐  ┌─────────────┐
   │  Service 1  │  │  Service 2  │  │  Service 3  │
   │  (Internal) │  │  (Internal) │  │  (Internal) │
   │  No Traefik │  │  No Traefik │  │  No Traefik │
   │  Exposure   │  │  Exposure   │  │  Exposure   │
   └─────────────┘  └─────────────┘  └─────────────┘
        All connected to doku-network
        Services call each other by container name
```

## Implementation Options

### Option 1: Spring Cloud Gateway Service

Add to `catalog.toml`:

```toml
[services.spring-gateway]
name = "Spring Cloud Gateway"
description = "API Gateway with auth and routing"
category = "gateway"
icon = "🚪"
tags = ["gateway", "spring", "api-gateway", "auth"]

[services.spring-gateway.links]
homepage = "https://spring.io/projects/spring-cloud-gateway"
documentation = "https://docs.spring.io/spring-cloud-gateway/docs/current/reference/html/"

[services.spring-gateway.versions."4.1"]
image = "your-org/spring-gateway:4.1"  # Your custom image
description = "Spring Cloud Gateway 4.1"
port = 8080
protocol = "http"
volumes = ["/app/config"]

[services.spring-gateway.versions."4.1".environment]
SPRING_PROFILES_ACTIVE = "local"
JWT_SECRET = "your-secret-key"
# Service URLs - using container names
SERVICE1_URL = "http://service1:8081"
SERVICE2_URL = "http://service2:8082"
SERVICE3_URL = "http://service3:8083"

[services.spring-gateway.versions."4.1".resources]
memory_min = "512m"
memory_max = "1g"
cpu_min = "0.5"
cpu_max = "1.5"
```

### Option 2: Kong API Gateway

```toml
[services.kong]
name = "Kong API Gateway"
description = "Cloud-native API Gateway"
category = "gateway"
icon = "🦍"
tags = ["gateway", "kong", "api-gateway"]

[services.kong.versions."3.4"]
image = "kong:3.4-alpine"
description = "Kong Gateway 3.4"
port = 8000
admin_port = 8001
protocol = "http"
volumes = ["/usr/local/kong/declarative"]

[services.kong.versions."3.4".environment]
KONG_DATABASE = "off"
KONG_DECLARATIVE_CONFIG = "/usr/local/kong/declarative/kong.yml"
KONG_PROXY_ACCESS_LOG = "/dev/stdout"
KONG_ADMIN_ACCESS_LOG = "/dev/stdout"
KONG_PROXY_ERROR_LOG = "/dev/stderr"
KONG_ADMIN_ERROR_LOG = "/dev/stderr"

[services.kong.versions."3.4".resources]
memory_min = "256m"
memory_max = "512m"
cpu_min = "0.25"
cpu_max = "1.0"
```

### Option 3: Traefik as API Gateway (Path-based Routing)

Use Traefik's advanced routing without separate gateway.

## Usage Patterns

### Pattern 1: Gateway + Backend Services

```bash
# 1. Install backend services WITHOUT Traefik exposure
doku install service1 --name user-service --no-expose
doku install service2 --name order-service --no-expose
doku install service3 --name payment-service --no-expose

# 2. Install API Gateway (ONLY gateway is exposed)
doku install spring-gateway --name api-gateway

# 3. Access via gateway only
curl https://api-gateway.doku.local/users
curl https://api-gateway.doku.local/orders
curl https://api-gateway.doku.local/payments
```

### Pattern 2: Service Discovery

Gateway configuration example:

**Spring Cloud Gateway `application.yml`:**
```yaml
spring:
  cloud:
    gateway:
      routes:
        - id: user-service
          uri: http://user-service:8081  # Container name
          predicates:
            - Path=/api/users/**
          filters:
            - StripPrefix=1
            - name: AuthenticationFilter  # Your custom filter

        - id: order-service
          uri: http://order-service:8082
          predicates:
            - Path=/api/orders/**
          filters:
            - StripPrefix=1
            - name: AuthenticationFilter
```

### Pattern 3: Environment Variable Configuration

```bash
# Install gateway with backend service URLs
doku install spring-gateway \
  --env USER_SERVICE_URL=http://user-service:8081 \
  --env ORDER_SERVICE_URL=http://order-service:8082 \
  --env PAYMENT_SERVICE_URL=http://payment-service:8083 \
  --env JWT_SECRET=your-secret
```

## Service Visibility Levels

### Public Services
- Exposed via Traefik
- Have their own subdomain
- Example: api-gateway.doku.local

### Internal Services
- NOT exposed via Traefik
- Only accessible within doku-network
- Called by container name
- Example: user-service:8081

## Implementation in Doku

We need to add support for internal services. Here's the enhancement:

### catalog.toml Enhancement

```toml
[services.myservice.versions."1.0"]
image = "myservice:1.0"
port = 8080
protocol = "http"

# NEW: Visibility configuration
[services.myservice.versions."1.0".exposure]
public = false  # Don't expose via Traefik
internal_only = true  # Only accessible within network
```

### Install Command Enhancement

```bash
# Install as internal service (no Traefik labels)
doku install myservice --internal

# Install as public service (with Traefik labels) - default
doku install api-gateway --public
```

## Complete Example: Microservices Setup

```bash
# Step 1: Setup Doku
doku init

# Step 2: Install PostgreSQL for shared database
doku install postgres:16 --name shared-db

# Step 3: Install backend services (internal only)
doku install user-service --name user-service --internal \
  --env DATABASE_URL=postgresql://shared-db:5432/users

doku install order-service --name order-service --internal \
  --env DATABASE_URL=postgresql://shared-db:5432/orders \
  --env USER_SERVICE_URL=http://user-service:8081

doku install payment-service --name payment-service --internal \
  --env DATABASE_URL=postgresql://shared-db:5432/payments \
  --env ORDER_SERVICE_URL=http://order-service:8082

# Step 4: Install API Gateway (public facing)
doku install spring-gateway --name api \
  --env USER_SERVICE_URL=http://user-service:8081 \
  --env ORDER_SERVICE_URL=http://order-service:8082 \
  --env PAYMENT_SERVICE_URL=http://payment-service:8083 \
  --env JWT_SECRET=local-dev-secret

# Step 5: Access everything through gateway
curl https://api.doku.local/api/users
curl https://api.doku.local/api/orders
curl https://api.doku.local/api/payments
```

## Service-to-Service Communication

Within doku-network, services communicate using container names:

```java
// In Order Service
RestTemplate restTemplate = new RestTemplate();
String url = "http://user-service:8081/users/{userId}";
User user = restTemplate.getForObject(url, User.class, userId);
```

```javascript
// In Node.js service
const axios = require('axios');
const response = await axios.get('http://payment-service:8083/payments');
```

## Authentication Flow

```
1. Client → https://api.doku.local/api/orders
2. Traefik → api-gateway:8080/api/orders
3. Gateway validates JWT token
4. Gateway routes → http://order-service:8082/orders
5. Order Service processes request
6. Order Service calls → http://user-service:8081/users/123
7. Response flows back through gateway
```

## Benefits

1. **Security**: Backend services not exposed externally
2. **Centralized Auth**: Single point for token validation
3. **Flexibility**: Easy to add/remove backend services
4. **Service Discovery**: Services find each other by name
5. **Local Development**: Mirrors production architecture
6. **Network Isolation**: All traffic stays in doku-network

## Advanced: Traefik Middlewares (Alternative)

If you don't want a separate gateway, use Traefik middlewares:

```yaml
# traefik-middlewares.yml
http:
  middlewares:
    auth-forward:
      forwardAuth:
        address: "http://auth-service:8080/validate"
        authResponseHeaders:
          - "X-User-Id"
          - "X-User-Role"

  routers:
    user-service:
      rule: "Host(`api.doku.local`) && PathPrefix(`/api/users`)"
      middlewares:
        - auth-forward
      service: user-service

    order-service:
      rule: "Host(`api.doku.local`) && PathPrefix(`/api/orders`)"
      middlewares:
        - auth-forward
      service: order-service
```

## Recommendations

For your use case with Spring API Gateway:

1. **Create custom Spring Gateway Docker image** with your config
2. **Add to catalog** with proper environment variables
3. **Install backend services with --internal flag**
4. **Install gateway as public service**
5. **Configure gateway** to route to services by container name
6. **Use single domain** (api.doku.local) for all API calls

This gives you production-like local development! 🚀
