# Salary API

## Author & Document Information

| Field | Value |
|------|------|
| Author | Ajitesh |
| Created On | 10-08-2023 |
| Version | v1.0 |
| Last Updated By | Neha |
| Last Edited On | 24-08-2023 |

---

## Purpose

The **Salary API** is a Java-based microservice developed as part of the **OT-Microservices stack**.  
The primary purpose of this application is to manage **salary-related transactions and records** in a scalable, secure, and independent manner.

### Why this application is needed
- To separate salary management from other HR functionalities
- To enable independent deployment and scaling of salary services
- To handle financial data with better performance and reliability
- To support high-volume salary queries using a distributed database

This application addresses challenges such as:
- Tight coupling in monolithic HR systems
- Limited scalability of legacy applications
- Lack of observability and standardized deployment processes

---

## Pre-Requisites

Before deploying the Salary API, ensure that the following **hardware, software, and security requirements** are met.

---

## System Requirements

### Hardware Specifications

| Hardware | Minimum Recommendation |
|--------|------------------------|
| Processor | Dual-core |
| RAM | 4 GB |
| Disk | 20 GB |
| OS | Ubuntu 22.04 |

---

## Dependencies

### Build Time Dependencies

| Name | Version | Description |
|----|--------|------------|
| Java (JDK) | 11 or above | Required to compile the application |
| Maven | 3.x | Build and dependency management |

---

### Run Time Dependencies

| Name | Version | Description |
|----|--------|------------|
| Java Runtime (JRE) | 11 or above | Required to run the application |
| ScyllaDB | Cassandra compatible | Primary database for salary data |
| Redis | Latest stable | Cache management |
| Migrate | Latest | Database schema migration |

---

### Other Dependencies

| Name | Version | Description |
|----|--------|------------|
| Prometheus | Optional | Metrics scraping |
| Swagger UI | Bundled | API documentation and testing |

---

## Important Ports

### Inbound Traffic

| Port | Description |
|----|------------|
| 9042 | Used by ScyllaDB |

### Outbound Traffic

| Port | Description |
|----|------------|
| 8080 | Used by embedded Tomcat server |

---

## Architecture

The Salary API follows a **microservices-based architecture**, where the service runs independently and communicates over HTTP.

**Key components:**
- Client (Frontend or API consumer)
- Salary API (Spring Boot application)
- Redis (Cache layer)
- ScyllaDB (Persistent data store)
- Monitoring systems

---

## Dataflow Diagram

### Data Flow Explanation

1. Client sends an HTTP request to the Salary API
2. Salary API checks Redis for cached data
3. If cache hit, the response is returned immediately
4. If cache miss, data is fetched from ScyllaDB
5. Retrieved data is cached in Redis
6. Response is returned to the client
7. Metrics are exposed for monitoring

---

## Step-by-Step Installation of Salary API

---

### Step 1: Installation of Software Dependencies

#### Build Dependencies

```bash
sudo apt update
sudo apt install -y openjdk-11-jdk maven
```

## Run Time Dependencies

The following runtime dependencies must be installed and configured before running the Salary API:

| Name | Version | Description |
|-----|--------|-------------|
| ScyllaDB | Cassandra compatible | Primary database used to store salary-related data |
| Redis | Latest stable | Cache manager for storing frequently accessed data |
| Migrate | Latest | Database migration tool used to manage schema changes |

> **Note:** Installation steps may vary based on the target environment (cloud or on-prem).

---

## Other Dependencies

The following dependencies are optional and are mainly used for monitoring and API validation:

| Name | Version | Description |
|-----|--------|-------------|
| Prometheus | Optional | Used for collecting application metrics |
| Swagger UI | Bundled | Provides interactive API documentation and testing interface |


## Step 2: Build / Artifact Generation

### Clone the Repository

```bash
git clone https://github.com/OT-MICROSERVICES/salary-api.git
cd salary-api
```

### Build the Application

```bash
make build
```

## Step 3: Application Deployment

### Run Database Migrations

```bash
make run-migrations
```

This initializes the database schema and tables.

### Start the Application

```bash
java -jar target/salary-0.1.0-RELEASE.jar
```

### Verify Application Status
```bash
curl http://localhost:8080/actuator/health

Expected output:

{
  "status": "UP"
}
```

## Monitoring
Monitoring ensures application health, performance, and availability.

## Metrics

| Parameter | Description | Priority | Threshold |
|---------|-------------|----------|-----------|
| Disk Utilization | Disk space used by application | High | > 90% |
| Availability | Application uptime | High | >= 99.9% |
| Memory Utilization | Memory usage | Medium | > 80% |
| CPU Utilization | CPU usage | Medium | > 70% |
| Network Traffic | Network usage | Medium | Varies |
| Latency | Response time | High | < 300ms |
| Errors | Error rate | High | > 5 per minute |
| Throughput | Requests per minute | High | > 1000 |
| Security | Authentication & access control | High | Continuous |

## Health Check

| Name | Type | InitialDelaySeconds | PeriodSeconds | TimeoutSeconds | SuccessThreshold | FailureThreshold |
|-----|------|---------------------|---------------|----------------|------------------|------------------|
| Salary API | ReadinessProbe | 10 | 10 | 5 | 1 | 3 |
| Salary API | LivenessProbe | 10 | 10 | - | 5 | 1 |

## Explanation of Health Parameters

| Parameter | Description |
|----------|-------------|
| ReadinessProbe | Checks if the application is ready to receive traffic |
| LivenessProbe | Checks if the application is running |
| InitialDelaySeconds | Delay before the first health check is performed |
| PeriodSeconds | Frequency at which health checks are executed |
| TimeoutSeconds | Maximum time to wait for a health check response |
| SuccessThreshold | Number of consecutive successful checks required |
| FailureThreshold | Number of consecutive failed checks allowed |

## Logging

| Log Type | Location | Description |
|---------|----------|-------------|
| Event Logs | location/to/event.log | Application-level events |
| Access Logs | location/to/access.log | Authentication and authorization logs |
| Server Logs | location/to/server.log | Server activity and runtime logs |
| Threat Logs | location/to/threat.log | Security-related events and threats |

## Disaster Recovery

Disaster Recovery (DR) ensures service restoration after unexpected events that impact application availability or data integrity.

### Disaster scenarios include:
- Hardware failures
- Network outages
- Security incidents
- Data corruption

### Recovery strategies include:
- Regular database backups
- Configuration and application backups
- Redeployment of the application on alternate infrastructure

---

## High Availability

High Availability (HA) focuses on minimizing application downtime and ensuring continuous service availability.

### High Availability is achieved by:
- Running multiple instances of the application
- Using load balancers to distribute traffic
- Ensuring database replication and redundancy

**Note:**  
Disaster Recovery focuses on restoring services *after a failure*,  
whereas High Availability focuses on *preventing downtime*.


## Troubleshooting

| Issue | Resolution |
|------|------------|
| Application not starting | Verify Java version and application configuration |
| Migration failure | Validate credentials and settings in `migration.json` |
| Redis connection error | Ensure Redis service is running and reachable |
| Health endpoint DOWN | Verify database connectivity and service status |

---

## FAQs

**Is this application free?**  
Yes, this is an open-source application.

**Can it be deployed on all cloud platforms?**  
Yes, the application is cloud-agnostic and can be deployed on any cloud platform.

**Is an enterprise version available?**  
No, only the open-source version is available.

---

## Contact Information

| Name | Email |
|-----|-------|
| Neha | neha@mygurukulam.co |

---

## References

| Link | Description |
|-----|-------------|
| https://www.jenkins.io/doc/book/installing/linux/#debianubuntu | Documentation format reference |
| https://amplifi.com/user-guide/FAQs.html | FAQ structure reference |
| https://thecontentauthority.com/blog/introduction-vs-overview | Intro vs Overview reference |
