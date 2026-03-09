# Python CI – Execution POC using Gunicorn / Flask

---

## Author Information

| Author | Created on | Version | Last updated by | Last edited on | L0 Reviewer | L1 Reviewer | L2 Reviewer |
|--------|------------|---------|-----------------|----------------|-------------|-------------|-------------|
| Hardik Modi | 05-03-2026 | v1.0 | Hardik Modi | 05-03-2026 | Sharvari Khamkar | Aman Raj | Abhishek Dubey / Ravindra Kumar |

---

## Table of Contents

1. [Purpose](#1-purpose)  
2. [Tools Used](#2-tools-used)  
3. [Implementation Steps](#3-implementation-steps)  
4. [Expected Output](#4-expected-output)  
5. [Benefits of CI Execution](#5-benefits-of-ci-execution)  
6. [Conclusion](#6-conclusion)  
7. [Contact Information](#7-contact-information)  
8. [References](#8-references)  

---

## 1. Purpose

The purpose of this POC is to demonstrate *Python CI execution validation* for the Attendance API and Notification Worker microservices.  

It ensures that:

- Python microservices start successfully in CI pipeline  
- Dependencies are installed correctly  
- Virtual environments work properly  
- Applications bind to the configured ports  

This validates readiness before production deployment.

---

## 2. Tools Used

| Tool | Purpose |
|------|----------|
| Python 3.11 | Runtime for microservices |
| Virtualenv / venv | Environment isolation |
| Poetry | Dependency management |
| pip | Dependency installation |
| Gunicorn | Production WSGI server |
| Flask | Web framework |
| GitHub Actions | CI automation |
| Ubuntu VM / EC2 | Execution environment |

---

## 3. Implementation Steps

### 3.1. Attendance Service Execution

Repository:  
https://github.com/OT-MICROSERVICES/attendance-api  

*Step 1 – Create Virtual Environment*

```bash
python3.11 -m venv venv
```

*Step 2 – Activate Environment*

```bash
source venv/bin/activate
```

*Step 3 – Activate Poetry Environment*
```bash
source /home/ubuntu/.cache/pypoetry/virtualenvs/attendance-api-ZqGHXPWO-py3.11/bin/activate
```

*Step 4 – Start Application using Gunicorn*
```bash
gunicorn app:app --log-config log.conf -b 0.0.0.0:8081
```

### 3.2. Notification Worker Execution

Repository:
https://github.com/OT-MICROSERVICES/notification-worker

*Step 1 – Create Virtual Environment*

```bash
python3.11 -m venv venv
```

*Step 2 – Activate Environment*

```bash
source venv/bin/activate
```
*Step 3 – Install Dependencies*

```bash
pip install -r requirements.txt
```
*Step 4 – Run Application*

```bash
python notification_api.py

```
## 4. Expected Output

*Attendance API:*

- Gunicorn starts successfully  
- Listening on port 8081  
- Worker booted successfully  

*Notification Worker:*

- Flask server starts  
- Listening on port 8090  
- Service initialized successfully  

Both microservices executed successfully, validating Python CI execution.

---

## 5. Benefits of CI Execution

- Validates microservice boot before production  
- Ensures environment reproducibility  
- Detects dependency errors early  
- Reduces runtime crashes in CI/CD  
- Confirms WSGI server readiness  
- Supports automated DevOps pipelines  

---

## 6. Conclusion

This POC demonstrates:

- Proper Python CI execution for Attendance and Notification services  
- Gunicorn and Flask applications successfully start in CI  
- Python microservices are ready for production deployment  

---

## 7. Contact Information

| Name        | Email                                      |
|-------------|--------------------------------------------|
| Hardik Modi | hardik.modi.snaatak@mygurukulam.co        |

---

## 8. References
| Reference | Description |
|-----------|-------------|
| [Attendance API Repository](https://github.com/Snaatak-Saarthi/documentation/blob/SCRUM-173-hardik/Applications/Python_CI_Checks/Code_Compilation/README.md) | Python Attendance microservice repository with CI compilation steps |
| [Notification Worker Repository](https://github.com/Snaatak-Saarthi/documentation/blob/SCRUM-173-hardik/Applications/Python_CI_Checks/Code_Compilation/README.md) | Python Notification Worker microservice repository with CI compilation steps |
| [Python Documentation](https://docs.python.org/3/) | Official Python documentation |
| [Gunicorn Documentation](https://gunicorn.org/) | WSGI server documentation |
| [Flask Documentation](https://flask.palletsprojects.com/) | Flask web framework documentation |
