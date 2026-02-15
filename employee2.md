# Employee-api Documentation

---

## Author Information

| Author | Created on | Version | Last updated by | Last edited on | L0 Reviewer | L1 Reviewer | L2 Reviewer |
|--------|------------|---------|-----------------|----------------|-------------|-------------|-------------|
| Hardik Modi | 07-02-2026 | v1.0 | Hardik Modi | 07-02-2026 | Sharvari Khamkar | Aman Raj | Abhishek Dubey / Ravindra Kumar |

---
## Table of Contents

1. [Introduction](#1-introduction)
2. [Purpose](#2-purpose)
3. [Pre-requisites](#3-pre-requisites)
   - [3.1 API Server Requirements](#31-api-server-requirements)
   - [3.2 Database Server Requirements](#32-database-server-requirements)
   - [3.3 Network & Connectivity Requirements](#33-network--connectivity-requirements)
   - [3.4 Security Requirements](#34-security-requirements)
4. [Dependencies](#4-dependencies)
5. [Important Ports](#5-important-ports)
6. [Architecture](#6-architecture)
7. [Dataflow Diagram](#7-dataflow-diagram)
8. [Step-by-step installation of employee-api](#8-step-by-step-installation-of-employee-api)
9. [Monitoring](#9-monitoring)
    - [9.1 Metrics](#91-metrics)
    - [9.2 Health Check](#92-health-check)
    - [9.3 Health Check Parameter Explanation](#93-health-check-parameter-explanation)
    - [9.4 Logging](#94-logging)
    - [9.5 Example Log Paths](#95-example-log-paths)
10. [Disaster Recovery](#10-disaster-recovery)
11. [High Availability](#11-high-availability)
12. [Troubleshooting](#12-troubleshooting)
13. [FAQs](#13-faqs)
14. [Contact Information](#14-contact-information)
15. [References](#15-references)

---

## 1. Introduction

The Employee API is a Golang-based microservice designed to manage employee data through secure and scalable REST endpoints. It acts as a centralized service for creating, retrieving, and managing employee information within a microservices architecture.

----

## 2. Purpose
The purpose of the Employee API is to provide a centralized and reliable service for managing employee-related data across the organization. It simplifies employee information handling by enabling scalable, consistent, and efficient data access in a microservices environment.

---
## 3. Pre-requisites

Before deploying the Employee API, ensure that the following infrastructure and system requirements are met.  
In this setup, the *Employee API and Database are hosted on separate servers*.

---

#### 3.1 API Server Requirements

| Hardware Specifications | Minimum Recommendation |
|-------------------------|------------------------|
| Processor | Dual-core |
| RAM | 4 GB |
| Disk | 20 GB |
| OS | Ubuntu 22.04 LTS |

#### 3.2 Database Server Requirements

| Hardware Specifications | Minimum Recommendation |
|-------------------------|------------------------|
| Processor | Dual-core |
| RAM | 8 GB |
| Disk | 50 GB |
| OS | Ubuntu 22.04 LTS |

---

#### 3.3 Network & Connectivity Requirements

- The API server must have *network access to the Database server*
- Required database ports (example: 9042 for ScyllaDB) must be *open between servers*
- Low-latency and reliable network connection is recommended for optimal performance

---

#### 3.4 Security Requirements

- Secure communication between API and Database servers
- Firewall rules allowing only required inbound/outbound traffic
- Database credentials stored securely (environment variables or config files)

## 4. Dependencies

### Build Time Dependencies

| Name | Version | Description |
|-----|---------|-------------|
| Go | As defined in go.mod | Programming language |
| Make | Latest | Build and automation tool |

---

### Run Time Dependencies

| Name | Version | Description |
|-----|---------|-------------|
| ScyllaDB | Latest | Primary database for storing employee data |
| Redis | Latest | Cache management system for faster responses |
| Swagger UI | Latest | API documentation and testing |
| Prometheus | Latest | Monitoring and metrics collection |

---
## 5. Important Ports

### Inbound Traffic

| Port | Description |
|------|-------------|
| 8080 | Application HTTP server |

---

### Outbound Traffic

| Port | Description |
|------|-------------|
| 9042 | ScyllaDB (if external access is required) |

---

### Others

| Others | Description |
|--------|-------------|
| Swagger UI | Accessible at /swagger/index.html |

---

### Additional Requirements

| Additional Requirements | Description |
|-------------------------|-------------|
| Database Access | ScyllaDB must be configured and reachable |
| Redis (Optional) | Required if caching is enabled |
| Config.yaml File | Mandatory for application initialization |

---
## 6. Architecture

<img width="831" height="564" alt="image" src="https://github.com/user-attachments/assets/4fccee90-d8e1-4108-be83-136787fb486b" />


The Employee API is a standalone microservice designed using a microservices-based architecture.  
It consists of the following core components:

- *Gin HTTP Framework*  
  Used as the REST server to handle incoming HTTP requests.

- *ScyllaDB*  
  Acts as the primary database for storing employee-related data.

- *Redis*  
  Used as a caching layer to improve application performance and reduce database load.

- *Prometheus*  
  Collects and monitors application metrics for observability.

- *Swagger UI*  
  Provides interactive API documentation and testing interface.

The service exposes REST endpoints under:

---
## 7. Dataflow Diagram

<img width="1443" height="600" alt="image" src="https://github.com/user-attachments/assets/fe85c4b1-323b-4bfe-a3fc-27e465fb13a9" />

The following steps describe how data flows through the Employee API:

1. The client sends an HTTP request to the Employee API  
   (for example: create employee, search employee).

2. The Gin router receives the request and routes it to the appropriate handler.

3. The handler processes the request and performs required operations on *ScyllaDB*.

4. *Redis cache* is queried or updated wherever applicable to improve performance.

5. The processed response is returned back to the client in JSON format.

---


## 8. Step-by-step installation of employee-api

### Step 1: Installation of software Dependencies
##### Build Dependency
bash
sudo apt update
sudo apt install golang make

### Step 2: Employee-api config.yml file setup
bash
# api config.yml
scylladb:
  host: ["10.0.1.25:9042"]
  username: scylla
  password: 12345
  keyspace: employee

redis:
  enabled: false
  host: 10.0.1.25:6379
  password: 12345
  database: 0

### Step 3: Employee-api database migration file setup  
bash 
migrate install
curl -L https://github.com/golang-migrate/migrate/releases/download/v4.17.0/migrate.linux-amd64.tar.gz | tar xvz
sudo mv migrate /usr/local/bin/migrate

bash
 migrate.json root
{
  "database": "cassandra://10.0.1.25:9042/employee?username=scylla&password=12345"
}

bash
make run-migrations

### Step 4: Background process run command (optional)
bash
nohup go run main.go > api.log 2>&1 &

### Step 5: Creating Services for the Employee-api 
bash
vim /etc/systemd/system/employee-api.services


bash
# service file for employee
[Unit]
Description=Employee API
After=network.target

[Service]
Type=simple
User=ubuntu
WorkingDirectory=/home/ubuntu/employee/employee-api
ExecStart=nohup go run /home/ubuntu/employee/employee-api/main.go > api.log 2>&1 &

Restart=always
RestartSec=3

[Install]
WantedBy=multi-user.target

bash
sudo systemctl daemon-reexec
sudo systemctl daemon-reload
sudo systemctl enable employee-api.service 
sudo systemctl start employee-api.service 
sudo systemctl status employee-api.service

## 9. Monitoring

Monitoring helps track system metrics and identify issues early to ensure application stability and performance.

---

#### 9.1 Metrics

| Parameter | Description | Priority | Threshold |
|----------|-------------|----------|-----------|
| Disk Utilization | Disk usage | High | > 90% |
| Availability | Application uptime | High | >= 99.9% |
| Memory Utilization | Memory consumption | Medium | > 80% |
| CPU Utilization | CPU usage | Medium | > 70% |
| Network Traffic | Network throughput | Medium | Varies |
| Latency | API response time | High | < 300 ms |
| Errors | Number of errors per minute | High | > 5/min |
| Throughput | Requests per minute | High | > 1000/min |
| Security | Authentication, authorization, encryption | High | Continuous |

---

#### 9.2 Health Check

| Name | Type | InitialDelaySeconds | PeriodSeconds | TimeoutSeconds | SuccessThreshold | FailureThreshold |
|------|------|--------------------|---------------|----------------|------------------|------------------|
| employee-api | ReadinessProbe | 10 | 10 | 5 | 1 | 3 |
| employee-api | LivenessProbe | 10 | 10 | - | 5 | 1 |

---

#### 9.3 Health Check Parameter Explanation

| Parameter | Description |
|----------|-------------|
| ReadinessProbe | Checks if the application is ready to receive traffic |
| LivenessProbe | Checks if the application is alive and responsive |
| InitialDelaySeconds | Time before the first health check |
| PeriodSeconds | Frequency of health checks |
| TimeoutSeconds | Maximum wait time before marking failure |
| SuccessThreshold | Consecutive successful checks to mark healthy |
| FailureThreshold | Consecutive failed checks to mark unhealthy |

---

#### 9.4 Logging

This application supports multiple types of logs for observability and auditing.

| Log Type | Description |
|---------|-------------|
| Event Logs | General application events |
| Authentication Logs | Access and authentication attempts |
| Server Logs | Server-side activities |
| Threat Logs | Security-related incidents |
| Access Logs | Client access records |

---
#### 9.5 Example Log Paths

text
/var/log/employee-api/server.log
/var/log/employee-api/access.log
/var/log/employee-api/threat.log


## 10. Disaster Recovery

Database backups should be taken regularly, and redundant instances should be deployed to restore services quickly after outages.  
ScyllaDB replication and Redis persistence are recommended for reliable data recovery.

---

## 11. High Availability

Deploy multiple instances of the Employee API behind a load balancer.  
If one instance fails, traffic is automatically routed to healthy instances.

---

## 12. Troubleshooting

### Common Issues

*Database connection failures*  
Ensure ScyllaDB is running and accessible from the API server.

*Redis errors*  
Verify Redis is running correctly or disable caching if it is not required.

*Port binding issues*  
Ensure port 8080 is not already in use.

---

## 13. FAQs

*Q: Is this application free?*  
Yes, this is an open-source application.

*Q: Can I deploy this application on any cloud platform?*  
Yes, it can be deployed on any cloud platform.

*Q: Is there an enterprise version available?*  
No, only the open-source version is available.

---

## 14. Contact Information

| Name | Email |
|------|-------|
| Hardik Modi | hardik.modi.snaatak@mygurukulam.co |

---

## 15. References

| Reference | Description |
|---------|-------------|
| [Employee API Repository](https://github.com/OT-MICROSERVICES/employee-api) | Source repository for the employee microservice |
