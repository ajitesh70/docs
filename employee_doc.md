# Employee API – Application Documentation

| Author | Created on | Version | Last updated by | Last edited on |
|--------|------------|---------|----------------|---------------|
| Ajitesh Singh | 11-02-2026 | v1.0 | Ajitesh Singh | 11-02-2026 |

---

## Purpose

Employee API is a **Golang-based microservice** responsible for managing employee-related operations within the OT-Microservices ecosystem.

### Why this application?

In a microservices architecture, employee data must be:

- Centrally managed
- Highly available
- Easily scalable
- Independently deployable

This API solves:

- Centralized employee record management
- Employee search and filtering
- Health monitoring and metrics exposure
- Clean integration with ScyllaDB and Redis

---

## Pre-requisites

Ensure the following requirements are met before deployment.

---

## System Requirements

| Hardware Specifications | Minimum Recommendation |
|------------------------|------------------------|
| Processor | Dual-core |
| RAM | 4GB |
| Disk | 20GB |
| OS | Ubuntu 22.04 |

---

## Dependencies

### Build Time Dependency

| Name | Version | Description |
|------|---------|-------------|
| Golang | 1.20+ | Programming language used to build API |
| Migrate | 4.15.2 | Database schema migration tool |
| Make | Latest | Build automation tool |

---

### Run Time Dependency

| Name | Version | Description |
|------|---------|-------------|
| ScyllaDB | 6.2 | Primary NoSQL database |
| Redis | Latest | Optional caching layer |

---

### Other Dependency

| Name | Version | Description |
|------|---------|-------------|
| jq | Latest | JSON processor |
| Git | Latest | Source code management |

---

## Important Ports

### Inbound Traffic

| Port | Description |
|------|-------------|
| 8080 | Employee API |
| 9042 | ScyllaDB |
| 6379 | Redis |
| 22 | SSH |

---

### Outbound Traffic

| Port | Description |
|------|-------------|
| 8080 | API communication |
| 9042 | Database communication |

---

## Architecture Overview

- Employee API (Golang-based service)
- ScyllaDB for persistent storage
- Redis for caching (optional)
- Migrate for schema versioning
- Systemd for service management

---

## Data Flow

1. Client sends request to Employee API (Port 8080)
2. API validates request
3. API checks Redis cache (if enabled)
4. If cache miss → Query ScyllaDB (Port 9042)
5. Response returned to client
6. Metrics exposed via `/metrics`

---

## Step-by-Step Installation of Employee API

### Step 1: Install Software Dependencies

#### Update System

```bash
sudo apt update
```

#### Install Build Dependencies

```bash
sudo apt install golang -y
sudo apt install make -y
sudo apt install jq -y
```

#### Install Migrate

```bash
curl -L https://github.com/golang-migrate/migrate/releases/download/v4.15.2/migrate.linux-amd64.tar.gz | tar xvz
sudo mv migrate /usr/local/bin/
migrate --version
```

#### Install Runtime Dependencies

Install ScyllaDB (Follow official documentation)

```bash
sudo apt install redis -y
```

---

### Step 2: Build / Artifact Generation

Clone Repository:

```bash
git clone https://github.com/OT-MICROSERVICES/employee-api
cd employee-api
```

Build Application:

```bash
go build -o employee-api
```

Run Database Migrations:

```bash
make run-migrations
```

---

### Step 3: Application Deployment

Run Application:

```bash
./employee-api
```

Verify Application:

```bash
curl http://localhost:8080/api/v1/employee/health
```

---

## Run API as Background Service

Create systemd Service File:

```bash
sudo vim /etc/systemd/system/employee.service
```

Add the following configuration:

```
[Unit]
Description=Employee API Service
After=network.target

[Service]
User=ubuntu
Group=ubuntu
WorkingDirectory=/home/ubuntu/application/employee-api
Environment=GIN_MODE=release
ExecStart=/home/ubuntu/application/employee-api/employee-api
Restart=always
RestartSec=3

[Install]
WantedBy=multi-user.target
```

Enable and Start Service:

```bash
sudo systemctl daemon-reexec
sudo systemctl daemon-reload
sudo systemctl enable employee
sudo systemctl start employee
```

Check Status:

```bash
sudo systemctl status employee
```

---

## API Endpoints

| Endpoint | Method | Description |
|----------|--------|------------|
| /metrics | GET | Application metrics |
| /api/v1/employee/health | GET | Basic health check |
| /api/v1/employee/health/detail | GET | Dependency health |
| /api/v1/employee/create | POST | Create employee |
| /api/v1/employee/search | GET | Search employee |
| /api/v1/employee/search/all | GET | Fetch all employees |
| /api/v1/employee/search/location | GET | Location-wise count |
| /api/v1/employee/search/designation | GET | Designation-wise count |
| /swagger/index.html | GET | Swagger documentation |

---

## Monitoring

Monitoring ensures system health, performance, and availability.

### Metrics

| Parameter | Description | Priority | Threshold |
|------------|------------|----------|------------|
| Disk Utilization | % disk used | High | >90% |
| Availability | Uptime | High | >= 99.9% |
| Memory Utilization | RAM usage | Medium | >80% |
| CPU Utilization | CPU usage | Medium | >70% |
| Latency | API response time | High | <300ms |
| Errors | Error rate | High | >5/min |
| Throughput | Requests handled | High | >1000/min |

---

## Health Check

| Name | Type | InitialDelaySeconds | PeriodSeconds | TimeoutSeconds | SuccessThreshold | FailureThreshold |
|------|------|--------------------|---------------|----------------|------------------|------------------|
| Employee API | ReadinessProbe | 10 | 10 | 5 | 1 | 3 |
| Employee API | LivenessProbe | 10 | 10 | 5 | 1 | 3 |

---

## Logging

| Log Type | Location | Description |
|----------|----------|------------|
| Event Logs | /var/log/employee/event.log | Application events |
| Access Logs | /var/log/employee/access.log | Authorization logs |
| Server Logs | /var/log/employee/server.log | Server operations |
| Threat Logs | /var/log/employee/threat.log | Security events |

---

## Disaster Recovery

- Daily ScyllaDB backups
- Snapshot-based recovery
- Version-controlled migrations
- Infrastructure-as-Code for redeployment

---

## High Availability

- Multiple API instances
- Load balancer in front
- ScyllaDB replication
- Redis clustering
- Health probes for automatic restart

**Note:**  
Disaster Recovery focuses on recovery after failure.  
High Availability focuses on preventing downtime.

---

## Troubleshooting

| Issue | Cause | Solution |
|-------|-------|----------|
| Port 8080 not accessible | Firewall blocked | Allow inbound rule |
| DB connection failure | Wrong host IP | Update config.yaml |
| Migration fails | Keyspace missing | Create keyspace manually |
| Service not starting | Incorrect working directory | Verify systemd path |

---

## FAQs

### Is this application free?

Yes, it is open-source.

### Can I deploy on any cloud?

Yes. AWS, Azure, GCP, or On-Prem.

### Is there an enterprise version?

No. This is an open-source contribution.

---

## Contact Information

| Name | Email |
|------|-------|
| Ajitesh Singh | ajitesh.singh.snaatak@mygurukulam.co |

---

## References

| Link | Description |
|------|------------|
| https://git-scm.com/doc | Git Documentation |
| https://docs.github.com | GitHub Documentation |
| https://opensource.docs.scylladb.com | ScyllaDB Installation Guide |

