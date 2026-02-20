# 📦 Liquibase Database Change Management – POC

**Author:** Ajitesh Singh  
**Version:** v1.0  
**Environment:** AWS EC2 Ubuntu 22.04  
**Database:** PostgreSQL 14  
**Liquibase Version:** 5.0.1  

---

## 1. Introduction

This Proof of Concept (POC) demonstrates how **Liquibase** manages database schema changes in a controlled, versioned, and automated manner.

Liquibase enables:

- Database schema version control
- Change tracking and auditing
- Safe and repeatable deployments
- Rollback support
- CI/CD integration capability

This POC was implemented on an EC2 Ubuntu 22.04 instance using PostgreSQL.

---

## 2. Architecture Overview

### Workflow Explanation

1. Developer writes ChangeLog file (XML).
2. ChangeSets define schema changes.
3. Liquibase CLI reads configuration from `liquibase.properties`.
4. JDBC driver connects to PostgreSQL.
5. Liquibase:
   - Checks `DATABASECHANGELOG`
   - Executes new changesets
   - Stores execution metadata and checksum
   - Prevents duplicate execution

### Metadata Tables

- `DATABASECHANGELOG` → Tracks executed changes
- `DATABASECHANGELOGLOCK` → Prevents concurrent execution

---

## 3. Pre-Requisites

| Component | Requirement |
|------------|-------------|
| OS | Ubuntu 22.04 |
| Java | OpenJDK 17 |
| Database | PostgreSQL 14 |
| Liquibase | 5.0.1 |
| JDBC Driver | PostgreSQL JDBC |
| Access | DB user with schema privileges |

---

## 4. Setup Guide

### 4.1 Install Java

```bash
sudo apt install openjdk-17-jdk -y
java -version
```

---

### 4.2 Install PostgreSQL

```bash
sudo apt install postgresql postgresql-contrib -y
sudo systemctl start postgresql
```

#### Create Database & User

```sql
CREATE DATABASE liquibase_poc;
CREATE USER liquibase_user WITH PASSWORD 'StrongPassword123';
GRANT ALL PRIVILEGES ON DATABASE liquibase_poc TO liquibase_user;
```

---

### 4.3 Install Liquibase

```bash
sudo apt update
sudo apt install liquibase -y
liquibase --version
```

---

### 4.4 Install PostgreSQL JDBC Driver

```bash
sudo apt install libpostgresql-jdbc-java -y
```

Verify:

```bash
ls /usr/share/java | grep postgresql
```

---

### 4.5 Project Structure

```
liquibase-poc/
│
├── liquibase.properties
└── db/
    └── changelog/
        └── db.changelog-master.xml
```

---

### 4.6 liquibase.properties

```properties
url=jdbc:postgresql://localhost:5432/liquibase_poc
username=liquibase_user
password=StrongPassword123
driver=org.postgresql.Driver
changeLogFile=db/changelog/db.changelog-master.xml
classpath=/usr/share/java/postgresql.jar
logLevel=info
```

---

### 4.7 Sample ChangeLog

```xml
<databaseChangeLog
    xmlns="http://www.liquibase.org/xml/ns/dbchangelog"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="
    http://www.liquibase.org/xml/ns/dbchangelog
    http://www.liquibase.org/xml/ns/dbchangelog/dbchangelog-4.3.xsd">

    <changeSet id="001-create-users-table" author="ajitesh">
        <createTable tableName="users">
            <column name="id" type="BIGINT">
                <constraints primaryKey="true" nullable="false"/>
            </column>
            <column name="username" type="VARCHAR(100)">
                <constraints nullable="false" unique="true"/>
            </column>
            <column name="created_at" type="TIMESTAMP"/>
        </createTable>

        <rollback>
            <dropTable tableName="users"/>
        </rollback>
    </changeSet>

</databaseChangeLog>
```

---

## 5. Execution

### Apply Migration

```bash
liquibase update
```

Expected Output:

```
Liquibase: Update has been successful.
```

---

### Validate Tables

```bash
psql -h localhost -U liquibase_user -d liquibase_poc
\dt
```

Tables created:

- users
- databasechangelog
- databasechangeloglock

---

## 6. Idempotency Validation

Run again:

```bash
liquibase update
```

Output:

```
Database is up to date, no changesets to execute
```

This confirms Liquibase prevents duplicate execution.

---

## 7. Rollback Validation

```bash
liquibase rollback-count 1
```

Verify:

```bash
\dt
```

Result:

- users table removed
- metadata tables remain

---

## 8. Backup Strategy

Before production deployment:

```bash
pg_dump -U postgres liquibase_poc > backup.sql
```

### Recommended Practices:

- Take full DB backup before release
- Use AWS snapshot in production
- Tag database releases

---

## 9. Best Practices

- Store changelogs in Git
- One logical change per changeset
- Never modify executed changesets
- Always include rollback block
- Test in lower environments first
- Use separate credentials per environment
- Automate execution in CI/CD

---

## 10. Conclusion

This POC demonstrates that Liquibase provides:

- Controlled database versioning
- Change traceability
- Safe rollback capability
- Repeatable deployments
- Production-ready database migration workflow

Liquibase successfully managed schema changes on EC2 Ubuntu with PostgreSQL and validated:

- Update execution
- Idempotency
- Rollback functionality

It is suitable for enterprise database release management.

---

## 11. Contact Information

Ajitesh Singh  
Email: ajitesh.singh.snaatak@mygurukulam.co  

---

## 12. References

- https://docs.liquibase.com  
- https://www.postgresql.org/docs/  
- https://github.com/liquibase/liquibase
