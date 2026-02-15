# Salary API – Proof of Concept (POC)

| Author | Created On | Version | Last Updated By | Reviewer L0 | Reviewer L1 | Reviewer L2 |
|--------|------------|---------|------------------|-------------|-------------|-------------|
| Ajitesh Singh | 05-02-2026 | v1.1 | Ajitesh Singh | Divya Mishra | Faisal | Mahesh |

---

# Table of Contents

1. [Purpose](#purpose)
2. [Project Overview](#project-overview)
3. [Architecture Overview](#architecture-overview)
4. [Prerequisites](#prerequisites)
5. [Dependencies](#dependencies)
6. [Configuration Changes](#configuration-changes)
7. [Build & Run Instructions](#build--run-instructions)
8. [Verification](#verification)
9. [API Endpoints](#api-endpoints)
10. [Deployment Details](#deployment-details)
11. [Troubleshooting](#troubleshooting)
12. [Conclusion](#conclusion)

---

# Purpose

This POC validates the successful deployment of the Salary Microservice in a microservices architecture. It ensures database connectivity, caching integration, API exposure, monitoring capability, and AWS deployment readiness.

---

# Project Overview

The Salary Microservice is developed using Spring Boot and is responsible for managing salary records in a distributed environment. It connects to Cassandra for persistent storage and Redis for caching to improve performance. The service is deployed on AWS EC2 and integrates with the frontend through Nginx reverse proxy.

---

# Architecture Overview

- Client sends request to Nginx
- Nginx forwards request to Salary API (Spring Boot)
- Salary API processes request
- Data is stored/retrieved from Cassandra
- Frequently accessed data is cached in Redis
- Actuator endpoints provide monitoring and health checks
- Service runs on AWS EC2 within private VPC network

---




 ## Prerequisites Installation
 Update System
``` 
sudo apt update
```
Install Java 17
```
sudo apt install openjdk-17-jdk -y
```

Verify:
```
java -version
```
<img width="645" height="159" alt="Screenshot 2026-02-11 191145" src="https://github.com/user-attachments/assets/f574fc0b-027c-4591-aaf3-75b1251b01e4" />
<img width="963" height="96" alt="Screenshot 2026-02-11 191321" src="https://github.com/user-attachments/assets/80fb4df1-ff06-463c-9510-dea8e8717ec2" />




 Install Git
 ```
sudo apt install git -y
```
 Clone Repository
```
git clone <repository-url>
cd salary-api
```
---

## Application.yaml configuration
 Server Configuration

```yaml
server:
  port: 8080
```

 Explanation

- The Salary API runs independently on **port 8080**.
- This allows it to operate as a standalone microservice.
- Nginx or API Gateway routes traffic to this port internally.


 Cassandra (ScyllaDB) Configuration

```yaml
spring:
  cassandra:
    keyspace-name: salary_db
    contact-points: 10.0.135.13
    port: 9042
    local-datacenter: datacenter1
```

 Explanation

| Property | Meaning |
|----------|----------|
| `10.0.135.13` | Private IP of ScyllaDB instance |
| `9042` | Default Cassandra port |
| `salary_db` | Dedicated keyspace for salary records |
| `datacenter1` | Cassandra local datacenter name |


  Redis Configuration

```yaml
spring:
  data:
    redis:
      host: 10.0.135.13
      port: 6379
      timeout: 2000
```
---

 ### Migration Configuration

 migration.json

```json
{
  "database": "cassandra://10.0.135.13:9042/salary_db"
}
```

---

  Purpose

- Defines Cassandra connection for schema migration
- Ensures `salary_db` keyspace exists before application startup
- Automatically creates/updates tables if required
- Maintains version-controlled database changes

---

##  Build & Run Instructions
###  Step 1: Build the Application

 Use Maven Wrapper (No need to install Maven)
```
chmod +x mvnw
./mvnw clean package -DskipTests
```

<img width="831" height="170" alt="Screenshot 2026-02-11 191520" src="https://github.com/user-attachments/assets/a912d183-1c88-482f-99d4-f1d7508467ce" />


###  Step 2: Run the Application

```bash
java -jar target/salary-0.1.0-RELEASE.jar
```
<img width="1274" height="514" alt="Screenshot 2026-02-11 200846" src="https://github.com/user-attachments/assets/18bc08a1-c700-4c29-b085-1bebf5cbc621" />


---

---

## Systemd Service Configuration (Production Setup)

To run the Salary API as a background service on AWS EC2:

### Step 1: Create Service File

```bash
sudo nano /etc/systemd/system/salary-api.service

[Unit]
Description=Salary API Spring Boot Service
After=network.target

[Service]
User=ubuntu
WorkingDirectory=/home/ubuntu/salary/salary-api
ExecStart=/usr/bin/java -jar target/salary-0.1.0-RELEASE.jar --server.port=8080
SuccessExitStatus=143
Restart=always
RestartSec=5

[Install]
WantedBy=multi-user.target
```
### Step 2: Reload and Start
```bash
sudo systemctl daemon-reload
sudo systemctl start salary-api
sudo systemctl enable salary-api
sudo systemctl status salary-api
```
<img width="1215" height="405" alt="Screenshot 2026-02-15 164523" src="https://github.com/user-attachments/assets/53ae01ba-dfc7-4d8b-b527-2f34ba5bfcb4" />

---

##  Verification Steps

###  Check Running Port

```bash
ss -lntp | grep 8080
```
<img width="1632" height="81" alt="Screenshot 2026-02-11 200812" src="https://github.com/user-attachments/assets/18f60e64-ad37-4b8d-a983-e953ff11277f" />

###  Health Check

```bash
curl http://localhost:8080/actuator/health
```
<img width="1905" height="267" alt="Screenshot 2026-02-11 200921" src="https://github.com/user-attachments/assets/1987fc73-7c6f-4be6-a505-909203d1adfa" />

**Expected Response:**

```json
{
  "status": "UP"
}
```

###  Fetch Salary Records

```bash
curl http://localhost:8080/api/v1/salary/search/all
```
<img width="1919" height="755" alt="Screenshot 2026-02-11 200954" src="https://github.com/user-attachments/assets/effffb6c-3b0b-4681-941b-a99f61e0009d" />

---

##  API Endpoints

| Endpoint | Method | Description |
|----------|--------|------------|
| `/api/v1/salary/create/record` | POST | Create Salary Record |
| `/api/v1/salary/search/all` | GET | Retrieve All Salary Records |
| `/actuator/health` | GET | Application Health |
| `/salary-documentation` | GET | Swagger UI |
| `/salary-api-docs` | GET | OpenAPI Docs |


---

## Troubleshooting

| Issue | Possible Cause |
|-------|----------------|
| Port 8080 already in use | Another process running on port 8080 |
| 404 Not Found | Incorrect API path or missing `/api/v1` in controller | 
| Cassandra connection failure | Wrong private IP or port 9042 blocked | 
| Redis connection failure | Redis not running or wrong host IP | 
| Security group blocking traffic | Required ports not allowed in inbound rules | 
| Nginx proxy not routing correctly | Incorrect `proxy_pass` configuration | 
| Frontend not reflecting API changes | Old React build being served |




## Conclusion

This POC demonstrates that the Salary Microservice runs independently, integrates with distributed storage systems, and is fully deployable in a production-ready AWS environment. It also validates that the service can scale independently within a microservices architecture.

---

## Contact Information

| Name | Email |
|------|-------|
| Ajitesh Singh | ajitesh.singh.snaatak@mygurukulam.co |

---
