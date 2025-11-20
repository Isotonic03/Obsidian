# Comprehensive Study Guide: API Gateway and Circuit Breakers

I'll break down this chapter into digestible sections, explaining each concept thoroughly for beginners.

---

## 1. **What is an Edge Service and API Gateway?**

### The Problem

Imagine you have multiple microservices (like Catalog Service for books, Order Service for purchases). Each has its own API. If clients (web browsers, mobile apps) had to:

- Remember all different service addresses
- Handle failures individually
- Implement security for each service

This would be **messy and complicated**.

### The Solution: API Gateway (Edge Service)

An **Edge Service** sits at the "edge" of your system - it's the single entry point for all external requests.

**Think of it like a hotel receptionist:**

- Clients only talk to the receptionist (Edge Service)
- The receptionist forwards requests to the right departments (internal services)
- The receptionist handles common tasks (security checks, visitor logs) for everyone

**Key Benefits:**

1. **Decoupling**: Clients don't need to know about internal service changes
2. **Single Entry Point**: One address to remember (like `http://myapp.com`)
3. **Cross-cutting Concerns**: Handle security, monitoring, rate limiting in one place
4. **Route Management**: Forward `/books` requests to Catalog Service, `/orders` to Order Service

---

## 2. **Spring Cloud Gateway Fundamentals**

### Core Building Blocks

Spring Cloud Gateway has three main components:

#### a) **Routes**

A route is like a road map with:

- **Unique ID**: Name for the route (e.g., "catalog-route")
- **URI**: Destination address (e.g., `http://localhost:9001/books`)
- **Predicates**: Conditions to match (like "if path starts with `/books`")
- **Filters**: Actions to perform before/after forwarding

**Example Configuration:**

```yaml
spring:
  cloud:
    gateway:
      routes:
        - id: catalog-route                          # Route name
          uri: http://localhost:9001/books           # Where to forward
          predicates:
            - Path=/books/**                         # Match requests to /books/*
```

**What happens**: When a request comes to `http://gateway:9000/books/123`, the gateway:

1. Checks predicates: "Does path start with `/books`?" → Yes!
2. Forwards to: `http://localhost:9001/books/123`

#### b) **Predicates**

Predicates are **conditions** that determine if a route should be used.

**Available predicates:**

- `Path`: Match URL path (e.g., `/books/**`)
- `Method`: Match HTTP method (GET, POST)
- `Header`: Match HTTP headers
- `Cookie`: Match cookies
- `Host`: Match hostname

**Think of predicates like "if statements":**

```
IF (request path starts with /books) THEN use catalog-route
```

#### c) **Filters**

Filters **modify requests or responses**.

**Two types:**

1. **Pre-filters**: Run BEFORE forwarding (modify request, check authentication)
2. **Post-filters**: Run AFTER receiving response (modify response, add headers)

**Common filters:**

- `Retry`: Retry failed requests
- `CircuitBreaker`: Stop calling failing services
- `RequestRateLimiter`: Limit number of requests
- `AddRequestHeader`: Add headers to requests

---

## 3. **Resilience Patterns**

### a) **Retry Pattern**

**The Problem**: Sometimes services fail temporarily (network glitch, service restart).

**The Solution**: Automatically retry the request.

**Configuration:**

```yaml
spring:
  cloud:
    gateway:
      default-filters:
        - name: Retry
          args:
            retries: 3                              # Try 3 times
            methods: GET                            # Only for GET requests
            series: SERVER_ERROR                    # Only on 5xx errors
            exceptions: 
              - java.io.IOException                 # Retry on these errors
              - java.util.concurrent.TimeoutException
            backoff:
              firstBackoff: 50ms                    # Wait 50ms before first retry
              maxBackoff: 500ms                     # Maximum wait time
              factor: 2                             # Double wait time each retry
```

**How backoff works:**

- 1st retry: Wait 50ms
- 2nd retry: Wait 100ms (50 × 2)
- 3rd retry: Wait 200ms (100 × 2)

**Important**: Don't retry on client errors (4xx) because they won't succeed anyway.

---

### b) **Circuit Breaker Pattern**

**The Analogy**: Like an electrical circuit breaker in your home that trips when there's an overload.

#### Three States:

**1. CLOSED (Normal Operation)**

- Requests flow normally
- System tracks failure rate
- If failures exceed threshold → OPEN

**2. OPEN (Service Down)**

- NO requests allowed through
- Immediately returns error or fallback
- Protects system from cascade failures
- After timeout → HALF_OPEN

**3. HALF_OPEN (Testing Recovery)**

- Allows limited test requests
- If successful → CLOSED
- If failed → OPEN

**Visual Flow:**

```
CLOSED → (too many failures) → OPEN → (wait time) → HALF_OPEN
   ↑                                                      ↓
   ←────────────── (success) ───────────── (failure) ────→
```

**Configuration:**

```yaml
resilience4j:
  circuitbreaker:
    configs:
      default:
        slidingWindowSize: 20                    # Track last 20 calls
        failureRateThreshold: 50                 # Trip at 50% failure
        waitDurationInOpenState: 15000           # Wait 15 seconds
        permittedNumberOfCallsInHalfOpenState: 5 # Test with 5 calls
```

**Example Scenario:**

1. Service receives 20 requests, 11 fail → Circuit trips to OPEN (>50% failed)
2. Next 100 requests immediately fail (circuit is open)
3. After 15 seconds → HALF_OPEN
4. Next 5 requests sent to test:
    - If 3+ succeed → CLOSED (back to normal)
    - If 3+ fail → OPEN (still broken)

**Fallback Method:** When circuit is open, execute backup logic:

```java
public RouterFunction<ServerResponse> routerFunction() {
    return RouterFunctions.route()
        .GET("/catalog-fallback", request ->
            ServerResponse.ok()
                .body(Mono.just(""), String.class))  // Return empty response
        .build();
}
```

---

### c) **Request Rate Limiter**

**The Problem**: Protect your system from being overwhelmed by too many requests.

**The Solution**: Limit requests per user/session in a time window.

**Token Bucket Algorithm:**

Imagine each user has a bucket:

1. Tokens are added to bucket at a fixed rate (replenish rate)
2. Bucket has maximum capacity (burst capacity)
3. Each request costs 1 token
4. No tokens = request rejected with HTTP 429 (Too Many Requests)

**Configuration:**

```yaml
spring:
  cloud:
    gateway:
      default-filters:
        - name: RequestRateLimiter
          args:
            redis-rate-limiter:
              replenishRate: 10        # Add 10 tokens/second
              burstCapacity: 20        # Maximum 20 tokens
              requestedTokens: 1       # Each request costs 1 token
```

**Example Timeline:**

```
Second 0: Bucket has 20 tokens (full)
- User sends 20 requests → All succeed (bucket now empty)

Second 1: 10 tokens added (replenish)
- User sends 15 requests → 10 succeed, 5 fail (HTTP 429)

Second 2: 10 tokens added
- User sends 8 requests → All succeed
```

**Why Redis?** Rate limiting needs to track state across multiple Edge Service instances. Redis provides:

- Fast in-memory storage
- Shared state across all instances
- High availability

---

## 4. **Timeouts**

**Two types of timeouts:**

### Connection Timeout

Time limit to **establish** connection.

```yaml
spring:
  cloud:
    gateway:
      httpclient:
        connect-timeout: 2000  # 2 seconds to connect
```

**Analogy**: How long you wait for someone to answer the phone.

### Response Timeout

Time limit to **receive** response.

```yaml
spring:
  cloud:
    gateway:
      httpclient:
        response-timeout: 5s  # 5 seconds to get response
```

**Analogy**: How long you wait for someone to reply after they answer.

---

## 5. **Redis Integration**

### What is Redis?

**Redis** = **RE**mote **DI**ctionary **S**erver

It's an **in-memory data store** (super fast) used for:

- **Caching**: Store frequently accessed data
- **Session Management**: Store user session data
- **Rate Limiting**: Track request counts

**Why use Redis?**

- **Speed**: Data in memory (microseconds access)
- **Shared State**: Multiple Edge Service instances share same data
- **Persistence**: Can save data to disk

### Setting up Redis with Docker

**Docker Compose Configuration:**

```yaml
services:
  polar-redis:
    image: "redis:7.0"           # Use Redis version 7.0
    container_name: "polar-redis"
    ports:
      - 6379:6379                # Expose Redis on port 6379
```

**Start Redis:**

```bash
docker-compose up -d polar-redis
```

### Spring Boot Redis Configuration

```yaml
spring:
  redis:
    host: localhost              # Redis server address
    port: 6379                   # Redis port
    connect-timeout: 2s          # Connection timeout
    timeout: 1s                  # Command timeout
```

---

## 6. **Session Management with Spring Session**

### The Problem

**Stateless applications** shouldn't store data in memory because:

- Data lost when instance shuts down
- Different instances have different data
- Users might hit different instances (load balancing)

### The Solution: Distributed Sessions

Store session data in Redis (external to application).

**Benefits:**

- Session survives application restarts
- All instances share same session data
- User experience is seamless

**Configuration:**

```yaml
spring:
  session:
    store-type: redis            # Use Redis for sessions
    timeout: 10m                 # Session expires after 10 minutes
    redis:
      namespace: polar:edge      # Prefix for session keys
```

**Gateway Filter:**

```yaml
spring:
  cloud:
    gateway:
      default-filters:
        - SaveSession              # Save session before forwarding
```

**Why SaveSession filter?** Ensures session is saved to Redis **before** forwarding request to downstream services.

---

## 7. **Docker Basics** (For Beginners)

### What is Docker?

**Docker** packages applications with all dependencies into **containers**.

**Analogy**: Like a shipping container that can be moved anywhere (laptop, server, cloud) and works the same.

### Key Concepts:

**1. Container**

- Running instance of an image
- Isolated environment
- Has its own filesystem, network

**2. Image**

- Blueprint for containers
- Contains application code + dependencies
- Immutable (read-only)

**3. Docker Compose**

- Tool to run multiple containers
- Define services in `docker-compose.yml`

### Basic Commands:

```bash
# Start services defined in docker-compose.yml
docker-compose up -d

# Stop services
docker-compose down

# View running containers
docker ps

# View logs
docker logs <container-name>
```

### Example: Running PostgreSQL

```yaml
services:
  polar-postgres:
    image: postgres:14.4                # Use PostgreSQL 14.4 image
    container_name: polar-postgres
    ports:
      - 5432:5432                       # Expose on port 5432
    environment:
      POSTGRES_USER: user               # Database username
      POSTGRES_PASSWORD: password       # Database password
      POSTGRES_DB: polardb             # Database name
```

**Start it:**

```bash
docker-compose up -d polar-postgres
```

**What happens:**

1. Docker downloads PostgreSQL image (if not present)
2. Creates container from image
3. Starts PostgreSQL server
4. Makes it accessible on port 5432

---

## 8. **Kubernetes Basics** (For Beginners)

### What is Kubernetes?

**Kubernetes (K8s)** orchestrates (manages) containers at scale.

**Analogy**: Like a conductor managing an orchestra of containers.

### Key Concepts:

**1. Pod**

- Smallest deployable unit
- Wraps one or more containers
- Has unique IP address

**2. Deployment**

- Manages Pods
- Ensures desired number of replicas
- Handles updates and rollbacks

**3. Service**

- Stable network endpoint for Pods
- Load balances traffic across Pods
- Types: ClusterIP, LoadBalancer, NodePort

**4. Ingress**

- Manages external access to services
- Routes traffic based on URL paths
- Handles TLS/SSL termination

**5. Namespace**

- Virtual cluster within cluster
- Isolates resources
- Examples: `default`, `ingress-nginx`

### Architecture Example:

```
Internet
   ↓
[Ingress] (polar-ingress)
   ↓
[Service] (edge-service) - ClusterIP
   ↓
[Deployment] (edge-service)
   ↓
[Pods] (edge-service-xxx, edge-service-yyy)
   ↓
[Containers] (Spring Boot application)
```

### Basic Commands:

```bash
# Create/update resources
kubectl apply -f deployment.yml

# View deployments
kubectl get deployments

# View pods
kubectl get pods

# View services
kubectl get services

# View logs
kubectl logs <pod-name>

# Delete resources
kubectl delete -f deployment.yml
```

---

## 9. **Kubernetes Ingress in Detail**

### What is Ingress?

**Ingress** manages external HTTP/HTTPS access to services.

**Without Ingress:**

- Each service needs own public IP (LoadBalancer)
- No centralized TLS management
- Expensive (multiple load balancers)

**With Ingress:**

- Single entry point (one public IP)
- Route based on URL paths/domains
- Centralized TLS/SSL
- Cost-effective

### Components:

**1. Ingress Resource** Defines routing rules (what you want).

**2. Ingress Controller** Enforces routing rules (how it's done).

Common controllers:

- NGINX Ingress
- Traefik
- HAProxy
- Ambassador

### Example Configuration:

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: polar-ingress
spec:
  ingressClassName: nginx              # Use NGINX controller
  rules:
    - http:
        paths:
          - path: /                    # Match all paths
            pathType: Prefix
            backend:
              service:
                name: edge-service     # Forward to this service
                port:
                  number: 80           # On this port
```

**Traffic Flow:**

```
User → http://myapp.com/books
  ↓
Ingress (checks rules)
  ↓
"Path starts with / → forward to edge-service:80"
  ↓
Edge Service (checks routes)
  ↓
"Path starts with /books → forward to catalog-service:9001"
  ↓
Catalog Service
```

### Setting up with Minikube:

**Minikube** = Local Kubernetes cluster for development.

```bash
# Start minikube cluster
minikube start --cpus 2 --memory 4g --profile polar

# Enable NGINX Ingress
minikube addons enable ingress --profile polar

# Get minikube IP (Linux)
minikube ip --profile polar

# Create tunnel (macOS/Windows)
minikube tunnel --profile polar
```

---

## 10. **Putting It All Together**

### Complete Request Flow:

```
1. User → http://polarbookshop.com/books (Browser)
   ↓
2. DNS → Resolves to Ingress IP
   ↓
3. Ingress → Checks rules, forwards to edge-service:80
   ↓
4. Edge Service → Applies filters:
   - RequestRateLimiter (check token bucket)
   - SaveSession (save session to Redis)
   - CircuitBreaker (check circuit state)
   - Retry (if needed)
   ↓
5. Route → Checks predicates, forwards to catalog-service:9001/books
   ↓
6. Catalog Service → Processes request, queries PostgreSQL
   ↓
7. Response ← Returns data
   ↓
8. Edge Service ← Post-filters (add headers, etc.)
   ↓
9. Ingress ← Forwards response
   ↓
10. User ← Receives response
```

### Resilience in Action:

**Scenario: Catalog Service is down**

```
Request arrives
  ↓
RequestRateLimiter: OK (has tokens)
  ↓
CircuitBreaker: CLOSED (check circuit state)
  ↓
Forward to Catalog Service
  ↓
Connection failed! (IOException)
  ↓
Retry #1: Failed
Retry #2: Failed
Retry #3: Failed
  ↓
CircuitBreaker: Register failure (1/20)
  ↓
All retries exhausted
  ↓
Execute fallback → Return empty response
  ↓
User receives empty list (graceful degradation)
```

**After 20 failed requests:**

```
CircuitBreaker: OPEN (failure rate > 50%)
  ↓
Next request arrives
  ↓
CircuitBreaker: OPEN (reject immediately)
  ↓
Execute fallback (no retry, no forward)
  ↓
User receives empty list (fast fail)
```

---

## 11. **Configuration Files Summary**

### application.yml (Complete Example)

```yaml
# Server configuration
server:
  port: 9000
  netty:
    connection-timeout: 2s
    idle-timeout: 15s
  shutdown: graceful

spring:
  application:
    name: edge-service
  
  # Redis configuration
  redis:
    host: localhost
    port: 6379
    connect-timeout: 2s
    timeout: 1s
  
  # Session management
  session:
    store-type: redis
    timeout: 10m
    redis:
      namespace: polar:edge
  
  # Gateway configuration
  cloud:
    gateway:
      # HTTP client settings
      httpclient:
        connect-timeout: 2000
        response-timeout: 5s
      
      # Routes
      routes:
        - id: catalog-route
          uri: http://localhost:9001/books
          predicates:
            - Path=/books/**
          filters:
            - name: CircuitBreaker
              args:
                name: catalogCircuitBreaker
                fallbackUri: forward:/catalog-fallback
        
        - id: order-route
          uri: http://localhost:9002/orders
          predicates:
            - Path=/orders/**
          filters:
            - name: CircuitBreaker
              args:
                name: orderCircuitBreaker
      
      # Default filters (applied to all routes)
      default-filters:
        - SaveSession
        - name: RequestRateLimiter
          args:
            redis-rate-limiter:
              replenishRate: 10
              burstCapacity: 20
              requestedTokens: 1
        - name: Retry
          args:
            retries: 3
            methods: GET
            series: SERVER_ERROR
            exceptions: 
              - java.io.IOException
              - java.util.concurrent.TimeoutException
            backoff:
              firstBackoff: 50ms
              maxBackoff: 500ms
              factor: 2

# Circuit breaker configuration
resilience4j:
  circuitbreaker:
    configs:
      default:
        slidingWindowSize: 20
        permittedNumberOfCallsInHalfOpenState: 5
        failureRateThreshold: 50
        waitDurationInOpenState: 15000
  timelimiter:
    configs:
      default:
        timeoutDuration: 5s

# Logging
logging:
  level:
    io.github.resilience4j: DEBUG
```

---

## 12. **Testing Your Understanding**

### Key Questions to Ask Yourself:

1. **What happens when a request arrives at Edge Service?**
    
    - Which filters are applied first?
    - How does routing work?
2. **What's the difference between Retry and Circuit Breaker?**
    
    - When to use each?
    - How do they work together?
3. **Why use Redis instead of storing data in application memory?**
    
    - What problems does it solve?
4. **What's the purpose of Ingress?**
    
    - How is it different from a Service?
    - Why not use LoadBalancer for each service?
5. **What happens when circuit breaker is OPEN?**
    
    - Are retries still attempted?
    - How does fallback work?

---

## 13. **Common Pitfalls and Best Practices**

### Pitfalls:

1. **Not configuring timeouts** → Requests hang indefinitely
2. **Retrying non-idempotent operations** → Don't retry POST/PUT/DELETE
3. **Circuit breaker without fallback** → User sees errors
4. **Wrong rate limit values** → System overload or poor UX
5. **Forgetting to save session** → Users logged out unexpectedly

### Best Practices:

1. **Always configure timeouts** for external calls
2. **Use circuit breakers** for downstream services
3. **Implement fallbacks** for graceful degradation
4. **Monitor metrics** (circuit breaker state, rate limits)
5. **Test failure scenarios** (shut down services, test behavior)
6. **Use separate Redis instances** for cache vs. sessions
7. **Keep applications stateless**, use data services for state

---

## Summary

This chapter covers building a resilient, scalable **Edge Service** using:

1. **Spring Cloud Gateway**: API gateway with routing, predicates, filters
2. **Resilience Patterns**: Retry, circuit breaker, rate limiting, timeouts
3. **Redis**: Fast in-memory storage for sessions and rate limiting
4. **Docker**: Containerization for easy deployment
5. **Kubernetes**: Container orchestration with Ingress for external access

The goal is to create a **single entry point** to your system that:

- Routes traffic intelligently
- Handles failures gracefully
- Protects from overload
- Provides consistent user experience

Study this guide section by section, try the examples, and make sure you understand each concept before moving to the next!