# Chapter 13: Observability and Monitoring - Study Guide

## Introduction

This chapter covers how to build **observable** cloud native applications using Spring Boot and the Grafana observability stack. Observability means we can infer the internal state of an application from its outputs. The chapter implements the 15-Factor methodology’s recommendations for telemetry.

-----

## Section 1: Logging with Spring Boot, Loki, and Fluent Bit

### 13.1.1 Logging with Spring Boot

**What are logs?**

- Discrete records of events that happened over time
- Include timestamp (when) and contextual information (what, which thread, which user)
- Categorized by severity: `trace`, `debug`, `info`, `warn`, `error`

**Spring Boot Logging:**

- Uses **Logback** by default via **SLF4J** (Simple Logging Facade for Java)
- Streams logs to **standard output** (following 15-Factor methodology)
- Default log level: `info` and above

**Configuring Loggers:**

```yaml
logging:
  level:
    io.github.resilience4j: debug
```

**Log Groups:**

```yaml
logging:
  group:
    circuitbreaker: io.github.resilience4j, org.springframework.cloud.circuitbreaker
  level:
    circuitbreaker: info
```

**Adding Logs in Code:**

```java
private static final Logger log = LoggerFactory.getLogger(BookController.class);

@GetMapping
public Iterable<Book> get() {
    log.info("Fetching the list of books in the catalog");
    return bookService.viewBookList();
}
```

### 13.1.2 Managing Logs with Loki, Fluent Bit, and Grafana

**Architecture:**

- **Fluent Bit**: Collects logs from all containers
- **Loki**: Aggregates and stores logs (makes them searchable)
- **Grafana**: Visualizes and queries logs

**Docker Configuration:**

```yaml
catalog-service:
  logging:
    driver: fluentd
    options:
      fluentd-address: 127.0.0.1:24224
```

**Query Example in Grafana:**

```
{container_name="/catalog-service"}
```

-----

## Section 2: Health Probes with Spring Boot Actuator and Kubernetes

### 13.2.1 Defining Health Probes

**Spring Boot Actuator** provides monitoring endpoints, including health checks.

**Add Dependency:**

```gradle
implementation 'org.springframework.boot:spring-boot-starter-actuator'
```

**Security Configuration:**

```java
.authorizeHttpRequests(authorize -> authorize
    .mvcMatchers("/actuator/**").permitAll()
    // other rules
)
```

**Expose Health Endpoint:**

```yaml
management:
  endpoints:
    web:
      exposure:
        include: health
  endpoint:
    health:
      show-details: always
      show-components: always
```

**Endpoint Response:**

```json
{
  "status": "UP",
  "components": {
    "db": {
      "status": "UP",
      "details": {
        "database": "PostgreSQL"
      }
    }
  }
}
```

### 13.2.2 Configuring Health Probes in Kubernetes

**Two Types of Probes:**

1. **Liveness Probe** (`/actuator/health/liveness`)

- Checks if application is in a valid internal state
- If DOWN → Kubernetes **restarts** the application

1. **Readiness Probe** (`/actuator/health/readiness`)

- Checks if application can handle requests
- If DOWN → Kubernetes **stops sending traffic** to that instance

**Enable Probes:**

```yaml
management:
  endpoint:
    health:
      probes:
        enabled: true
```

**Kubernetes Deployment Configuration:**

```yaml
livenessProbe:
  httpGet:
    path: /actuator/health/liveness
    port: 9001
  initialDelaySeconds: 10
  periodSeconds: 5
readinessProbe:
  httpGet:
    path: /actuator/health/readiness
    port: 9001
  initialDelaySeconds: 5
  periodSeconds: 15
```

**Custom Readiness Configuration (Edge Service with Redis):**

```yaml
management:
  endpoint:
    health:
      group:
        readiness:
          include: readinessState,redis
```

-----

## Section 3: Metrics with Spring Boot Actuator, Prometheus, and Grafana

### 13.3.1 Configuring Metrics with Actuator and Micrometer

**What are metrics?**

- Numeric data measured at regular intervals
- Track events, count items, measure time, monitor resources

**Micrometer:**

- Provides vendor-neutral façade (like SLF4J for logging)
- Auto-configured by Spring Boot Actuator
- Instruments JVM, Spring MVC, data sources, etc.

**Add Prometheus Support:**

```gradle
runtimeOnly 'io.micrometer:micrometer-registry-prometheus'
```

**Expose Prometheus Endpoint:**

```yaml
management:
  endpoints:
    web:
      exposure:
        include: health, prometheus
```

**Add Application Tag:**

```yaml
management:
  metrics:
    tags:
      application: ${spring.application.name}
```

**Sample Metric Output:**

```
jvm_threads_states_threads{application="catalog-service",state="waiting"} 13.0
```

### 13.3.2 Monitoring Metrics with Prometheus and Grafana

**Architecture:**

- **Prometheus**: Scrapes (pulls) metrics from applications, stores them
- **Grafana**: Visualizes metrics in dashboards, sets alerts

**Query Example:**

```
jvm_memory_used_bytes{application="catalog-service"}
```

**Dashboards Available:**

- JVM Dashboard (CPU, memory, GC, threads)
- Application-specific metrics

### 13.3.3 Prometheus in Kubernetes

**Deployment Annotations:**

```yaml
template:
  metadata:
    annotations:
      prometheus.io/scrape: "true"
      prometheus.io/path: /actuator/prometheus
      prometheus.io/port: "9001"
```

-----

## Section 4: Distributed Tracing with OpenTelemetry and Tempo

### 13.4.1 Distributed Tracing Concepts

**Key Terms:**

- **Trace**: Activities for a request, identified by trace ID
- **Span**: Each step in processing, identified by trace ID + span ID
- **Tags**: Metadata (URI, username, tenant)

**Example Flow (Edge Service → Catalog Service):**

1. Span 1: Edge Service accepts HTTP request
2. Span 2: Edge Service routes to Catalog Service
3. Span 3: Catalog Service handles request

**Technology Stack:**

- **OpenTelemetry**: Standard for generating/propagating traces (CNCF project)
- **Tempo**: Aggregates and stores traces (push-based)
- **Grafana**: Queries and visualizes traces

### 13.4.2 Configuring OpenTelemetry in Spring Boot

**Add Dependency:**

```gradle
ext {
  set('otelVersion', "1.17.0")
}

dependencies {
  runtimeOnly "io.opentelemetry.javaagent:opentelemetry-javaagent:${otelVersion}"
}
```

**Enhanced Log Format (includes trace/span IDs):**

```yaml
logging:
  pattern:
    level: "%5p [${spring.application.name},%X{trace_id},%X{span_id}]"
```

**Log Output Example:**

```
[catalog-service,d9e61c8cf853fe7fdf953422c5ff567a,eef9e08caea9e32a]
```

**Docker Configuration:**

```yaml
environment:
  - JAVA_TOOL_OPTIONS=-javaagent:/workspace/BOOT-INF/lib/opentelemetry-javaagent-1.17.0.jar
  - OTEL_SERVICE_NAME=catalog-service
  - OTEL_EXPORTER_OTLP_ENDPOINT=http://tempo:4317
  - OTEL_METRICS_EXPORTER=none
```

**Correlation in Grafana:**

- View logs in Loki
- Click trace ID → Navigate to related trace in Tempo
- See all steps across distributed system

-----

## Section 5: Application Management with Spring Boot Actuator

### 13.5.1 Monitoring Flyway Migrations

**Expose Flyway Endpoint:**

```yaml
management:
  endpoints:
    web:
      exposure:
        include: flyway, health, prometheus
```

**Response Example:**

```json
{
  "migrations": [
    {
      "description": "Initial schema",
      "script": "V1__Initial_schema.sql",
      "state": "SUCCESS",
      "version": "1",
      "installedOn": "2022-03-19T17:06:54Z"
    }
  ]
}
```

### 13.5.2 Exposing Application Information

**Custom Properties:**

```yaml
info:
  system: Polar Bookshop

management:
  endpoints:
    web:
      exposure:
        include: info
  info:
    env:
      enabled: true
    java:
      enabled: true
    os:
      enabled: true
```

**Build Information (Gradle):**

```gradle
springBoot {
  buildInfo()
}
```

**Response Example:**

```json
{
  "build": {
    "artifact": "catalog-service",
    "version": "0.0.1-SNAPSHOT"
  },
  "system": "Polar Bookshop",
  "java": {
    "version": "17.0.3"
  }
}
```

### 13.5.3 Heap Dumps for Memory Analysis

**Expose Endpoint:**

```yaml
management:
  endpoints:
    web:
      exposure:
        include: heapdump
```

**Generate Heap Dump:**

```bash
http --download :9001/actuator/heapdump
```

**Analysis Tools:**

- VisualVM
- JDK Mission Control

-----

## Summary of Key Technologies

|Technology              |Purpose                                |Type     |
|------------------------|---------------------------------------|---------|
|**Fluent Bit**          |Log collection                         |Collector|
|**Loki**                |Log aggregation & storage              |Backend  |
|**Prometheus**          |Metrics collection & storage           |Backend  |
|**Tempo**               |Trace aggregation & storage            |Backend  |
|**Grafana**             |Visualization for logs, metrics, traces|Frontend |
|**Micrometer**          |Metrics instrumentation façade         |Library  |
|**OpenTelemetry**       |Distributed tracing standard           |Library  |
|**Spring Boot Actuator**|Production-ready endpoints             |Library  |

-----

## Important Actuator Endpoints Summary

|Endpoint                    |Purpose                     |
|----------------------------|----------------------------|
|`/actuator/health`          |Overall health status       |
|`/actuator/health/liveness` |Kubernetes liveness probe   |
|`/actuator/health/readiness`|Kubernetes readiness probe  |
|`/actuator/prometheus`      |Metrics in Prometheus format|
|`/actuator/flyway`          |Database migration status   |
|`/actuator/info`            |Application information     |
|`/actuator/heapdump`        |Generate heap dump          |

-----

This structured guide covers all major concepts from Chapter 13, maintaining the book’s terminology and examples while organizing them for easier study and understanding.​​​​​​​​​​​​​​​​