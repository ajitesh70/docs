# Domain/Security | DNS/SSL | SSL POC
---
## Document Control
| **Author** | **Created On** | **Version** | **Last Updated By** | **Reviewer L0** | **Reviewer L1** | **Reviewer L2** |
|-----------|---------------|-------------|---------------------|----------------|----------------|----------------|
| Anirudh | 08-02-2026 | v1.0 | Anirudh | Prince | Aditya | Piyush |
---
## Table of Contents
1. [Introduction](#introduction)  
2. [POC Objective](#poc-objective)  
3. [Prerequisites](#prerequisites)  
4. [SSL Setup Details](#ssl-setup-details)  
5. [Step 1: Verify DNS Resolution](#step-1-verify-dns-resolution)  
6. [Step 2: Install Web Server (Nginx)](#step-2-install-web-server-nginx)  
7. [Step 3: Verify Web Server](#step-3-verify-web-server)  
8. [Step 4: Install Certbot](#step-4-install-certbot)  
9. [Step 5: Generate SSL Certificate](#step-5-generate-ssl-certificate)  
10. [Step 6: Verify HTTPS Access](#step-6-verify-https-access)  
11. [Step 7: Verify SSL Certificate Details](#step-7-verify-ssl-certificate-details)  
12. [Conclusion](#conclusion)
13. [Reference Table](#reference-table)
---
## Introduction
This document provides a **Proof of Concept (POC)** for configuring **SSL/TLS** on a domain. The objective is to validate secure HTTPS communication using a trusted SSL certificate and capture execution proof through screenshots.
---
## POC Objective
| Objective | Description |
|----------|-------------|
| **SSL Setup** | Configure SSL for a domain |
| **Security Validation** | Ensure HTTPS is enabled |
| **Evidence** | Capture screenshots for verification |
| **Outcome** | Secure encrypted communication |
---
## Prerequisites
| Requirement | Description |
|------------|-------------|
| **Domain Name** | Registered domain |
| **DNS Access** | Ability to update DNS records |
| **Server** | Linux VM / Cloud instance |
| **Web Server** | Nginx or Apache |
| **Open Ports** | 80 and 443 |
---
## SSL Setup Details
| Item | Value |
|-----|------|
| **SSL Provider** | Let’s Encrypt |
| **SSL Tool** | certbot |
| **Validation Type** | HTTP-01 |
| **Web Server** | Nginx |
---
## Step 1: Verify DNS Resolution
Check whether the domain resolves to the server IP.
```bash
nslookup yourdomain.com
```
<img width="1010" height="171" alt="image" src="https://github.com/user-attachments/assets/08b155d7-c734-434e-9bb5-b37b19caa9e3" />
---
## Step 2: Install Web Server (Nginx)
Update the package repository and install the Nginx web server.
```bash
sudo apt update
sudo apt install nginx -y
```
<img width="1415" height="457" alt="image" src="https://github.com/user-attachments/assets/a5ffec75-a7ec-405a-b371-2d49f39e7cb8" />
---
## Step 3: Verify Web Server
Verify that the Nginx web server is running successfully.
```bash
systemctl status nginx
```
<img width="1747" height="389" alt="image" src="https://github.com/user-attachments/assets/a2acb89a-8fb4-4279-ac40-a4256a31d3c4" />
---
## Step 4: Install Certbot
Install Certbot and the Nginx plugin to enable SSL certificate generation.
```bash
sudo apt install certbot python3-certbot-nginx -y
```
<img width="1467" height="365" alt="image" src="https://github.com/user-attachments/assets/f7175905-1831-4725-a6c6-3a558878be03" />
---
## Step 5: Generate SSL Certificate
Generate an SSL/TLS certificate for the domain using Certbot with the Nginx plugin.
```bash
sudo certbot --nginx -d yourdomain.com
```
<img width="1499" height="383" alt="image" src="https://github.com/user-attachments/assets/77420ef1-23de-4b53-8c2f-e820aba43a3d" />
---
## Step 6: Verify HTTPS Access
Verify that HTTPS is enabled and the SSL certificate is working correctly.
Open the following URL in a web browser:
<img width="1909" height="966" alt="image" src="https://github.com/user-attachments/assets/4c243163-bdd3-44a4-97d7-29b3a1895ba5" />
---
## Step 7: Verify SSL Certificate Details
Verify the SSL/TLS certificate details and secure connection using OpenSSL.
```bash
openssl s_client -connect yourdomain.com:443
```
<img width="1911" height="908" alt="image" src="https://github.com/user-attachments/assets/d292a3ca-5af9-4e1e-83f6-118f23fd4d6c" />
---
## Conclusion
This Proof of Concept (POC) confirms that SSL/TLS has been successfully configured for the domain. Secure HTTPS communication is enabled, the SSL certificate has been issued and validated, and encrypted traffic is verified using both browser-based and command-line checks.
---
## Reference Table
| Reference Type | Description | Link |
|---------------|-------------|------|
| SSL Documentation | Conceptual DNS & SSL documentation used as reference for this POC | https://github.com/Snaatak-Error-404/Sprint-1/blob/SCRUM-120-neha/Domain_Security%20/SSL/Documentation%20/README.md |
