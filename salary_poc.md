#  Salary API – Proof of Concept (POC)

| Author | Created On | Version | Last Updated By | Reviewer L0 | Reviewer L1 | Reviewer L2 |
|--------|------------|---------|------------------|-------------|-------------|-------------|
| Ajitesh Singh | 5-02-2026 | v1.1 | Ajitesh Singh | Divya Mishra | Faisal | Mahesh |
---

##  Project Overview

This Proof of Concept (POC) demonstrates the implementation and deployment of a **Salary Microservice** built using **Spring Boot** following microservices architecture principles.

The service:

- Stores and manages salary records
- Uses Cassandra as the primary database
- Uses Redis for caching
- Exposes REST APIs
- Provides health & monitoring endpoints
- Deploys on AWS EC2
- Integrates with frontend via Nginx reverse proxy

---

##  Architecture Overview

###  Components

| Component     | Purpose |
|--------------|----------|
| Spring Boot  | Backend REST API |
| Cassandra    | Primary Database |
| Redis        | Caching Layer |
| Nginx        | Reverse Proxy |
| AWS EC2      | Hosting Infrastructure |

###  High-Level Flow

Client → Nginx → Spring Boot (Salary API) → Cassandra  
                                     ↘ Redis (Cache)

---

##  Tech Stack

- Java 17
- Spring Boot 3.x
- Cassandra
- Redis
- Maven Wrapper (`./mvnw`)
- AWS EC2
- Nginx
- Actuator (Monitoring)

---

##  Application Configuration

###  `src/main/resources/application.yml`

```yaml
server:
  port: 8080

spring:
  application:
    name: salary-api

  cassandra:
    keyspace-name: salary_db
    contact-points: 10.0.135.13
    port: 9042
    local-datacenter: datacenter1
    schema-action: none

  data:
    redis:
      host: 10.0.135.13
      port: 6379
      timeout: 2000

management:
  endpoints:
    web:
      base-path: /actuator
      exposure:
        include: health,prometheus,metrics

  endpoint:
    health:
      show-details: always
```

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
##  Build & Run Instructions
###  Step 1: Build the Application

⚠ Use Maven Wrapper (No need to install Maven)
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

##  Deployment Details

- Salary API deployed on **AWS EC2**
- Cassandra & Redis hosted internally
- Nginx configured as reverse proxy
- Spring Boot running on port **8080**

###  Security Group Configuration

| Port | Service |
|------|---------|
| 8080 | Salary API |
| 9042 | Cassandra |
| 6379 | Redis |

---

##  Issues Faced During POC

- Port 8080 already in use
- Incorrect Nginx proxy IP configuration
- Frontend not rebuilding after API change
- Security group blocking internal traffic
- Cassandra connectivity errors

---

##  Key Learnings

- Importance of internal vs public IP usage
- How reverse proxy routing works
- Spring Boot health endpoint debugging
- Redis integration with Spring Data
- React build process & API integration
- Security Group troubleshooting in AWS

---

##  POC Result

- Salary API successfully built  
- Cassandra connected  
- Redis connected  
- Health endpoint responding  
- Salary data fetch working  
- Ready for frontend integration  

---

##  Scalability & Production Readiness

- Microservice architecture
- Independent deployment capability
- Monitoring enabled via Actuator
- Caching layer for performance optimization
- Ready for containerization (Docker/Kubernetes)

---

## Conclusion

This POC demonstrates that the Salary Microservice runs independently, integrates with distributed storage systems, and is fully deployable in a production-ready AWS environment. It also validates that the service can scale independently within a microservices architecture.

---

## Contact Information

| Name | Email |
|------|-------|
| Ajitesh Singh | ajitesh.singh.snaatak@mygurukulam.co |

---
