# Liquibase Database Change Management

---

## Author Information

| Author | Created on | Version | Last updated by | Last edited on | L0 Reviewer | L1 Reviewer | L2 Reviewer |
|--------|------------|---------|-----------------|----------------|-------------|-------------|-------------|
| Ajitesh Singh | 07-02-2026 | v1.0 | Ajitesh Singh | 07-02-2026 | Priyanshu | Faisal | Mahesh |

---

## Table of Contents
1. [Introduction](#1-introduction)  
2. [Pre-requisites](#2-pre-requisites)  
3. [Liquibase Architecture](#3-liquibase-architecture)  
4. [Step-by-Step Setup Guide (POC)](#4-step-by-step-setup-guide-poc)  
   - 4.1 [Install Liquibase](#41-install-liquibase)  
   - 4.2 [Database Configuration](#42-database-configuration-postgresql)  
   - 4.3 [Create Database and User](#43-create-database-and-user)  
   - 4.4 [Create Changelog](#44-create-changelog)  
   - 4.5 [Apply Database Changes](#45-apply-database-changes)  
   - 4.6 [Verify Changes](#46-verify-changes)  
5. [Backup and Rollback Strategy](#5-backup-and-rollback-strategy)  
6. [Best Practices](#6-best-practices)  
7. [POC Validation Checklist](#7-poc-validation-checklist)  
8. [Conclusion](#8-conclusion)  
9. [Contact Information](#9-contact-information)  
10. [References](#10-references)  
---

## 1. Introduction

The purpose of this document is to explain the Liquibase workflow, which manages, tracks, and applies database schema changes in a controlled and automated manner to ensure consistency, traceability, and safe deployments across all environments.

It ensures:
- Consistency across environments (Dev, QA, Staging, Prod)
- Traceability of database changes
- Safe rollbacks and auditability
- Integration with CI/CD pipelines

Liquibase works using *changelog files* that describe database changes in a structured and versioned format.

---

## 2. Pre-requisites

| Component | Requirement |
|---------|-------------|
| Operating System | Linux / Windows / macOS |
| Java | JDK 8 or above |
| Database | MySQL / PostgreSQL / Oracle / SQL Server |
| Access | DB user with schema change permissions |
| Tools | Git, CLI access |
| Optional | CI/CD tool (Jenkins/GitHub Actions/GitLab CI) |


## 3. Liquibase Architecture

<img width="980" height="461" alt="image" src="https://github.com/user-attachments/assets/e40e8302-dffc-4245-9bbd-4628d84d1afa" />

## Liquibase Architecture (Short Overview)

Liquibase is a database schema migration tool that manages and tracks database changes in a controlled way.

### How It Works
- Developers define database changes in *ChangeLogs* (XML, YAML, JSON, or SQL).
- Changes are organized into *Change Sets* (e.g., create table, add column).
- Liquibase is executed using the *CLI or API*.
- The *Liquibase Engine* converts change sets into database-specific SQL.
- SQL is executed via the database *driver (JDBC)*.

### Database Tracking
- *DATABASECHANGELOG*: Stores applied change sets to avoid re-execution.
- *DATABASECHANGELOGLOCK*: Prevents concurrent executions.

### Key Benefits
- Database-independent migrations
- Version-controlled schema changes
- Safe, repeatable deployments
---



## 4. Step-by-Step Setup Guide (POC)

## 4.1 Install Liquibase

### Install Liquibase on Linux (Ubuntu/RHEL)

#### Step 1: Verify java (mandatory)
- Java version check  
bash
sudo apt install openjdk-17-jdk -y
sudo yum install java-17-openjdk -y
java --version

<img width="1379" height="284" alt="image" src="https://github.com/user-attachments/assets/66d85b7f-78be-4bba-a42f-9ba53e349660" />

- Add Liquibase repository
bash
sudo apt update
sudo apt install wget gnupg -y
wget -qO- https://repo.liquibase.com/liquibase.asc | sudo gpg --dearmor -o /usr/share/keyrings/liquibase-archive-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/liquibase-archive-keyring.gpg] https://repo.liquibase.com stable main" | sudo tee /etc/apt/sources.list.d/liquibase.list


<img width="1494" height="89" alt="image" src="https://github.com/user-attachments/assets/caf3a60d-fd01-4509-aa4c-2f4b5b9b63d9" />


- Liquibase install
bash
sudo apt update
sudo apt install liquibase -y
liquibase --version


<img width="1060" height="329" alt="image" src="https://github.com/user-attachments/assets/4dd0e79c-286c-4941-b91c-0ee39e589e50" />

---

### 4.2 Database Configuration (PostgreSQL)
bash
sudo apt update
sudo apt install postgresql postgresql-contrib -y

<img width="1378" height="220" alt="image" src="https://github.com/user-attachments/assets/93182a8d-cfee-4c23-b7b9-d7d7701c1fd5" />

bash
sudo systemctl status postgresql

<img width="866" height="205" alt="image" src="https://github.com/user-attachments/assets/2b3bd179-32d9-4a21-9eb8-248a667ae17b" />


### 4.3 Create Database and User

#### Step 1: Switch to postgres user

bash
sudo -i -u postgres
psql
CREATE DATABASE liquibase_poc;
CREATE USER liquibase_user WITH PASSWORD 'StrongPassword123';
GRANT ALL PRIVILEGES ON DATABASE liquibase_poc TO liquibase_user;
\q


<img width="747" height="234" alt="image" src="https://github.com/user-attachments/assets/b221c236-5e5e-411f-8b52-78d0b0bf8000" />


### 4.4 Create Changelog

- Create master changelog file
- Add first changeset
- Use unique id and author

### sample file
sample file
<?xml version="1.0" encoding="UTF-8"?>
<databaseChangeLog
        xmlns="http://www.liquibase.org/xml/ns/dbchangelog"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="
           http://www.liquibase.org/xml/ns/dbchangelog
           http://www.liquibase.org/xml/ns/dbchangelog/dbchangelog-4.27.xsd">

    <changeSet id="001-create-users-table" author="hardik">
        <createTable tableName="users">
            <column name="id" type="BIGINT">
                <constraints primaryKey="true" nullable="false"/>
            </column>
            <column name="username" type="VARCHAR(100)">
                <constraints nullable="false" unique="true"/>
            </column>
            <column name="created_at" type="TIMESTAMP"/>
        </createTable>
    </changeSet>

</databaseChangeLog>


<img width="907" height="418" alt="image" src="https://github.com/user-attachments/assets/0b662067-cab4-4ac6-b200-d19a9b8a0bb4" />

bash 
nano ~/liquibase-poc/liquibase.properties

bash
url=jdbc:postgresql://localhost:5432/liquibase_poc
username=lbuser
password=lbpass
driver=org.postgresql.Driver
changeLogFile=db/changelog/db.changelog-master.xml
logLevel=info


<img width="676" height="186" alt="image" src="https://github.com/user-attachments/assets/d479544e-6ea8-4ccf-bac0-9908292e0432" />

---

### 4.5 Apply Database Changes

- Run Liquibase update command
bash
liquibase update

<img width="1248" height="811" alt="image" src="https://github.com/user-attachments/assets/cad10a6c-0160-4188-8438-08655513cdee" />

- Observe execution logs
- Verify metadata tables creation

<img width="1010" height="402" alt="image" src="https://github.com/user-attachments/assets/00da4813-a54e-4be9-b156-7b8033e60b2e" />

---

### 4.6 Verify Changes

- Validate table/schema changes
- Query database objects
- Confirm applied changes

<img width="975" height="236" alt="image" src="https://github.com/user-attachments/assets/b7b2ef1e-40ac-403d-9eab-ca0e56a8fc26" />

---

## 5. Backup and Rollback Strategy

### Backup Strategy
- Take full DB backup before execution
bash
pg_dump -h localhost -U lbuser liquibase_poc > liquibase_poc_backup.sql

<img width="978" height="117" alt="image" src="https://github.com/user-attachments/assets/34ddf5c5-d7cd-4a75-a355-08fa37f195fb" />

- Enable automated backups in production
- Store backups securely
<img width="727" height="408" alt="image" src="https://github.com/user-attachments/assets/9c73f9e0-7a56-4ad6-a222-22d85a67dc39" />
<img width="698" height="488" alt="image" src="https://github.com/user-attachments/assets/55026608-b335-4668-bf18-5a632837674f" />
<img width="829" height="163" alt="image" src="https://github.com/user-attachments/assets/6afcfd76-b309-4e22-a60e-85b530b553a7" />



### Rollback Strategy
- Use rollback tags or rollback count
<img width="1361" height="889" alt="image" src="https://github.com/user-attachments/assets/61050082-3cc4-4250-8a60-752c8eac695b" />


- Define rollback logic in changesets
<img width="878" height="567" alt="image" src="https://github.com/user-attachments/assets/657f9214-8f24-4562-9862-37ae2932643a" />

<img width="512" height="191" alt="image" src="https://github.com/user-attachments/assets/a805f33b-3b8a-4f41-8f7d-1a947f7f8159" />

bash
liquibase update

<img width="496" height="230" alt="image" src="https://github.com/user-attachments/assets/a1891ae2-ceb3-40be-8ee4-729a546937b8" />


---

## 6. Best Practices

- Always use version control for changelogs
- One logical change per changeset
- Never modify executed changesets
- Use rollback blocks for every change
- Test in lower environments first
- Follow naming conventions
- Automate via CI/CD

---

## 7. POC Validation Checklist

- Liquibase installed successfully  
- Database connection verified  
- Changelog executed successfully  
- Metadata tables created  
- Schema changes validated  
- Rollback tested  

---

## 8. Conclusion

Liquibase enables reliable, repeatable, and auditable database deployments. By adopting Liquibase, organizations can treat database changes as code, reduce deployment risks, and ensure consistency across environments.

It plays a critical role in modern DevOps and CI/CD practices.

---

## 9. Contact Information

| Name | Email |
|------|------|
| Ajitesh Singh | ajitesh.singh.snaatak@mygurukulam.co |

---

## 10. References

| Reference | Description |
|----------|-------------|
| [Liquibase Official Documentation](https://docs.liquibase.com/) | Official Liquibase concepts, commands, changelogs, and rollback documentation |
| [PostgreSQL Documentation](https://www.postgresql.org/docs/) | PostgreSQL database configuration, administration, and best practices |
| [Liquibase GitHub Repository](https://github.com/liquibase/liquibase) | Liquibase source code, issues, and community contributions |

---
