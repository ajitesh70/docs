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
5. [Best Practices](#6-best-practices)  
6. [POC Validation Checklist](#7-poc-validation-checklist)  
7. [Conclusion](#8-conclusion)  
8. [Contact Information](#9-contact-information)  
9. [References](#10-references)  
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

![WhatsApp Image 2026-02-17 at 9 31 07 PM](https://github.com/user-attachments/assets/2f7564e6-33ec-4954-bf62-c12fe3763f28)



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
```bash
sudo apt install openjdk-17-jdk -y
sudo yum install java-17-openjdk -y
java --version
```
<img width="1731" height="350" alt="Screenshot 2026-02-18 143758" src="https://github.com/user-attachments/assets/588a622b-6cb8-4603-8eb6-78e052ad6aeb" />




- Add Liquibase repository
```bash
sudo apt update
sudo apt install wget gnupg -y
wget -qO- https://repo.liquibase.com/liquibase.asc | sudo gpg --dearmor -o /usr/share/keyrings/liquibase-archive-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/liquibase-archive-keyring.gpg] https://repo.liquibase.com stable main" | sudo tee /etc/apt/sources.list.d/liquibase.list
```

<img width="1882" height="107" alt="Screenshot 2026-02-18 145905" src="https://github.com/user-attachments/assets/5e6bc66a-acaf-48ca-8b7a-ff29f788e2ab" />






- Liquibase install
```bash
sudo apt update
sudo apt install liquibase -y
liquibase --version
```

<img width="1313" height="420" alt="Screenshot 2026-02-18 145953" src="https://github.com/user-attachments/assets/18bd1de7-c12e-4490-8b42-7452780d444f" />



---

### 4.2 Database Configuration (PostgreSQL)
```bash
sudo apt update
sudo apt install postgresql postgresql-contrib -y
```

<img width="1725" height="268" alt="Screenshot 2026-02-18 150128" src="https://github.com/user-attachments/assets/4d939b04-aee8-46f7-b8b0-60978061d724" />



```bash
sudo systemctl status postgresql
```

<img width="1073" height="247" alt="Screenshot 2026-02-18 150157" src="https://github.com/user-attachments/assets/0c402d95-9a4f-4419-a9c2-bd39c7632926" />



### 4.3 Create Database and User

#### Step 1: Switch to postgres user

```bash
sudo -i -u postgres
psql
CREATE DATABASE liquibase_poc;
CREATE USER liquibase_user WITH PASSWORD 'StrongPassword123';
GRANT ALL PRIVILEGES ON DATABASE liquibase_poc TO liquibase_user;
\q
```

<img width="912" height="267" alt="Screenshot 2026-02-18 150226" src="https://github.com/user-attachments/assets/c4b2de16-7c0f-42e5-8fb2-7048021e3ef9" />




### 4.4 Create Changelog

- Create master changelog file
- Add first changeset
- Use unique id and author

### sample file

```
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
```

<img width="1080" height="503" alt="Screenshot 2026-02-18 150255" src="https://github.com/user-attachments/assets/ea73573b-a4f8-44ba-a525-b4db537bd21f" />



```bash 
nano ~/liquibase-poc/liquibase.properties
```
```bash
url=jdbc:postgresql://localhost:5432/liquibase_poc
username=lbuser
password=lbpass
driver=org.postgresql.Driver
changeLogFile=db/changelog/db.changelog-master.xml
logLevel=info
```

<img width="838" height="225" alt="Screenshot 2026-02-18 150633" src="https://github.com/user-attachments/assets/0bf66e3b-2866-4496-a2a7-281c2b75f0af" />



---

### 4.5 Apply Database Changes

- Run Liquibase update command
```bash
liquibase update
```
<img width="1326" height="855" alt="Screenshot 2026-02-18 150654" src="https://github.com/user-attachments/assets/76ed3f25-6910-4130-a5ec-9d7673b34557" />



- Observe execution logs
- Verify metadata tables creation

<img width="1250" height="486" alt="Screenshot 2026-02-18 150719" src="https://github.com/user-attachments/assets/643b250b-7c63-41f6-acbc-fdcde92a4224" />



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
