# Setup and Run the App for POC — Frontend Application

## Author Information

| Author | Created on | Version | Last updated by | Last edited on | L0 Reviewer | L1 Reviewer | L2 Reviewer |
|--------|------------|---------|-----------------|----------------|-------------|-------------|-------------|
| Hardik Modi | 04-02-2026 | v1.0 | Hardik Modi | 16-01-2026 | Sharvari Khamkar | Aman Raj | Abhishek Dubey / Ravindra Kumar |


## Table of Contents

1. [Purpose](#1-purpose)  
2. [POC Architecture (AWS)](#2-poc-architecture-aws)  
   2.1 [Architecture Diagram](#21-architecture-diagram)  
   2.2 [Dataflow Diagram](#22-dataflow-diagram)  
3. [Prerequisites](#3-prerequisites)  
4. [Deployment Steps](#4-deployment-steps)      
5. [Troubleshooting](#5-troubleshooting)  
6. [Final Result](#6--final-result)  
7. [Final Deployment Summary](#7--final-deployment-summary)  
8. [Conclusion](#8--conclusion)  
9. [Contact Information](#9-contact-information)  
10. [References](#10-references)  

---

## 1. Purpose

The purpose of this Proof of Concept (POC) is to demonstrate the deployment of a multi-tier application architecture on AWS, where the Frontend application is hosted securely inside a private subnet and communicates with backend services through controlled internal networking.

---

## 2. POC Architecture (AWS)

| Component | Location | Role |
|------------|-----------|------|
| Bastion + NGINX | Public Subnet | Public entry point |
| Frontend Server | Private Subnet | Hosts frontend |
| API Server | Private Subnet | Hosts all APIs |
| Redis Middleware | Private Subnet | Caching layer |
| DB Server | Private Subnet | ScyllaDB + PostgreSQL |

### 2.1 Architecture Diagram

<img width="1184" height="395" alt="image" src="https://github.com/user-attachments/assets/ff5637ce-ea73-447f-8c60-0c795f60caa5" />



---

### 2.2 Dataflow Diagram

Flow:- The request first comes to the Bastion server, where NGINX forwards it to the appropriate API service. The API checks Redis to see if the data is cached.

If data is found in Redis, it is returned immediately. If not, the API fetches it from ScyllaDB or PostgreSQL, stores it in Redis, and then sends the response back.

<img width="1302" height="479" alt="image" src="https://github.com/user-attachments/assets/e22581e2-5fdb-4261-afd5-7da3e2eb6ede" />


---

## 3. Prerequisites

| Requirement | Version |
|------------|----------|
| Ubuntu | 22.04 |
| NodeJS | 18+ |
| NGINX | Latest |
| Redis | 7+ |
| ScyllaDB | Latest |
| PostgreSQL | 16 |

---

## 4. Deployment Steps

### Step 1: Create and Access EC2 Instances

Create following instances:

1. Bastion Server (Public Subnet)
   - Port 22 from your IP
   - Port 80 open

2. Frontend Server (Private Subnet)
   - Port 22 from Bastion
   - Port 3000 internal only

3. API Server (Private Subnet)
   - Ports 8080, 8081, 8082

4. Redis Middleware Server (Private Subnet)
   - Port 6379 from API only

5. DB Server (Private Subnet)
   - ScyllaDB 9042
   - PostgreSQL 5432

SSH into Bastion:

bash
ssh -i key.pem ubuntu@<BASTION_PUBLIC_IP>


### Step 2:  Connect to Frontend Instance via bastion 
 
bash
ssh -i projectkey.pem ubuntu@<Frontend_private_IP>

---

### Step 3 – Install Node.js 16

Project uses older React + Webpack dependencies.  
Node 16 ensures stable builds.

#### Install Node 16

bash
curl -fsSL https://deb.nodesource.com/setup_16.x | sudo -E bash -
sudo apt install nodejs -y


#### Verify Installation

bash
node -v
npm -v


Expected:

v16.x.x

<img width="407" height="154" alt="image" src="https://github.com/user-attachments/assets/c23049e3-a1aa-43f9-9172-d7851290bff0" />

---

### Step 4 – Clone Frontend Repository

bash
git clone https://github.com/OT-MICROSERVICES/frontend.git
cd frontend


<img width="478" height="151" alt="image" src="https://github.com/user-attachments/assets/da5370bb-ce63-43db-ba5f-6772b1a4fc27" />

### Step 5 – Build Production Version and run time services

bash
npm run build


Making Service of frontend:

bash
sudo cat /etc/systemd/system/frontend.service 


bash 
[Unit]
Description=Frontend React App (serve)
After=network.target

[Service]
Type=simple
User=ubuntu
WorkingDirectory=/home/ubuntu/frontend
ExecStart=/home/ubuntu/.nvm/versions/node/v16.20.2/bin/serve -s build -l 3000
Restart=always
RestartSec=5
Environment=NODE_ENV=production
Environment=PATH=/home/ubuntu/.nvm/versions/node/v16.20.2/bin:/usr/bin:/bin

[Install]
WantedBy=multi-user.target

bash
sudo systemctl daemon-reload
sudo systemctl start frontend
sudo systemctl enable frontend
sudo systemctl status frontend

<img width="924" height="427" alt="image" src="https://github.com/user-attachments/assets/770dd296-61de-4c62-b8d6-64e379603a39" />

---
### Step 6 – Install Nginx on bastion server

bash
sudo apt update
sudo apt install nginx -y

---
### Step 7 – Configure Nginx (Reverse Proxy + Static Hosting)

bash
server {
    server_name otmicroservice.gaganawasthi.online;

    # =======================
    # FRONTEND (React)
    # =======================
    location / {
        proxy_pass http://10.0.3.250:3000;
        proxy_http_version 1.1;

        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    # =======================
    # EMPLOYEE API
    # =======================
    location /api/v1/employee/ {
        proxy_pass http://10.0.2.75:8080/api/v1/employee/;
        proxy_http_version 1.1;

        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;

        proxy_connect_timeout 10s;
        proxy_send_timeout 30s;
        proxy_read_timeout 30s;
    }

    # =======================
    # SALARY API
    # =======================
    location /api/v1/salary/ {
        proxy_pass http://10.0.2.75:8082/api/v1/salary/;
        proxy_http_version 1.1;

        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;

        proxy_connect_timeout 10s;
        proxy_send_timeout 30s;
        proxy_read_timeout 30s;
    }

    # =======================
    # ATTENDANCE API
    # =======================
    location /api/v1/attendance/ {
        proxy_pass http://10.0.2.75:8081/api/v1/attendance/;
        proxy_http_version 1.1;

        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    listen 443 ssl; # managed by Certbot
    ssl_certificate /etc/letsencrypt/live/otmicroservice.gaganawasthi.online/fullchain.pem; # managed by Certbot
    ssl_certificate_key /etc/letsencrypt/live/otmicroservice.gaganawasthi.online/privkey.pem; # managed by Certbot
    include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot

}
server {
    if ($host = otmicroservice.gaganawasthi.online) {
        return 301 https://$host$request_uri;
    } # managed by Certbot


    listen 80;
    server_name otmicroservice.gaganawasthi.online;
    return 404; # managed by Certbot


}

---
### Step 8 – Enable Configuration

bash
sudo ln -s /etc/nginx/sites-available/frontend /etc/nginx/sites-enabled/
sudo rm /etc/nginx/sites-enabled/default


Test configuration:

bash
sudo nginx -t


Reload Nginx:

bash
sudo systemctl reload nginx


---
### Step 9 – API Proxy Testing

Test from bastion server:

bash
curl -s -L https://otmicroservice.gaganawasthi.online/api/v1/salary/search/all | jq

<img width="1058" height="281" alt="image" src="https://github.com/user-attachments/assets/88a0ebdc-83bd-4954-896c-6a4e02c7f823" />

bash
curl -s -L https://otmicroservice.gaganawasthi.online/api/v1/employee/search/all | jq

<img width="1096" height="499" alt="image" src="https://github.com/user-attachments/assets/4aef0dfe-3ee1-41af-a5a4-887d4e7ddc95" />

bash
curl -s -L https://otmicroservice.gaganawasthi.online/api/v1/attendance/search/all | jq

<img width="1121" height="270" alt="image" src="https://github.com/user-attachments/assets/4a0dbfb0-a1ed-48b8-bfb6-898b9b5fcb57" />

---

### Step 10 – Access in Browser
#### Before Access there is we have to make changes in fronend server .js file of every particular api on following path

#### EmployeeData Route Fix (Overview)
bash
nano frontend/src/EmployeeData.js


javascript
fetch("/api/v1/employee/search/all")

<img width="1602" height="672" alt="image" src="https://github.com/user-attachments/assets/73894047-b2be-485b-b936-1889d53e2697" />



####  EmployeeForm.js Route Fix
bash
nano frontend/src/EmployeeForm.js


javascript
fetch("/api/v1/employee/create"....
fetch("/api/v1/salary/create/record".....
fetch("/api/v1/notification/send"....



<img width="1579" height="936" alt="image" src="https://github.com/user-attachments/assets/f8b35b02-8c8a-47f2-a9f9-7c7d0d1ef3f7" />

####  EmployeeList.js Route Fix

 bash
nano frontend/src/ListEmployee.js

 bash
 fetch("/api/v1/salary/search/all")


<img width="1724" height="849" alt="image" src="https://github.com/user-attachments/assets/97d3a77a-bc2d-4a90-95a5-b3b0e639be16" />

#### salary_api Route Fix

bash
nano frontend/src/ListSalary.js


javascript
 fetch("/api/v1/salary/search/all")


<img width="1413" height="629" alt="image" src="https://github.com/user-attachments/assets/33f039a3-0a28-48d3-9ab9-1dab111c549f" />

#### AttendanceForm Route Fix
bash
nano frontend/src/AttendanceForm.js


javascript
  fetch("/api/v1/attendance/create".....

<img width="1363" height="824" alt="image" src="https://github.com/user-attachments/assets/2a262c80-3626-42b0-b8c2-215780a66530" />

#### AttendanceList Route Fix
bash
nano frontend/src/AttendanceList.js


javascript
  fetch("/api/v1/attendance/search/all")


<img width="1276" height="765" alt="image" src="https://github.com/user-attachments/assets/12b45006-438d-40d5-9ed7-b0355aeb1c34" />

---

## 5. Troubleshooting

| Issue | Check |
|-------|-------|
| Frontend not loading | Check NGINX status (sudo systemctl status nginx) |
| 502 Bad Gateway | Verify frontend server is running and accessible |
| API not reachable | Check Security Groups and firewall rules |
| Redis error | Verify redis.conf bind address and requirepass configuration |
| DB connection failed | Check pg_hba.conf (PostgreSQL) and scylla.yaml (ScyllaDB) configuration |

## 6.  Final Result

- React production build served via Nginx
- Reverse proxy routing working
- Employee API integrated
- Attendance API integrated
- Salary API integrated
- Dashboard charts working
- Salary table rendering correctly
- Clean production environment

---

## 7.  Final Deployment Summary

- Node 16 used for compatibility
- React app built in production mode
- Nginx configured as reverse proxy
- Multi-microservice routing enabled
- All src changes aligned with backend APIs
- Permission and routing issues resolved

---

## 8.  Conclusion

This POC demonstrates:

- End-to-end frontend deployment
- Multi-service reverse proxy architecture
- Production build hosting
- API path correction and integration
- Source-level corrections for full functionality
- Real-world debugging and resolution workflow

---

## 9. Contact Information

| Name | Email |
|------|-------|
| Hardik Modi | hardik.modi.snaatak@mygurukulam.co |

---

## 10. References

| Reference | Description |
|-----------|-------------|
| [NGINX Documentation](https://nginx.org/en/docs/) | Official documentation for installing, configuring, and managing NGINX web server and reverse proxy |
| [Redis Documentation](https://redis.io/docs) | Official guide for Redis installation, configuration, and usage as an in-memory data store |
| [ScyllaDB Documentation](https://docs.scylladb.com) | Official documentation for ScyllaDB setup, architecture, and administration |
| [PostgreSQL Documentation](https://www.postgresql.org/docs) | Official PostgreSQL documentation covering installation, configuration, and database management |
| [Node.js Documentation](https://nodejs.org/en/docs) | Official documentation for Node.js runtime environment, APIs, and application development |
