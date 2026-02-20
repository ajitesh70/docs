#  Liquibase Database Change Management – POC

 
## Author Information

| Author | Created on | Version | Last updated by | Last edited on | L0 Reviewer | L1 Reviewer | L2 Reviewer |
|--------|------------|---------|-----------------|----------------|-------------|-------------|-------------|
| Ajitesh Singh | 07-02-2026 | v1.0 | Ajitesh Singh | 07-02-2026 | Divya | Faisal | Mahesh |

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

![WhatsApp Image 2026-02-17 at 9 31 07 PM](https://github.com/user-attachments/assets/2f7564e6-33ec-4954-bf62-c12fe3763f28)

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

The following versions were used and tested in this POC environment.

| Component | Tested Version | Minimum Recommended |
|------------|----------------|--------------------|
| OS | Ubuntu 22.04 | Ubuntu 20.04+ |
| Java | OpenJDK 17 | Java 11+ |
| Database | PostgreSQL 14 | PostgreSQL 12+ |
| Liquibase | 5.0.1 | Liquibase 4.x+ |
| JDBC Driver | PostgreSQL JDBC 42.3.x | Compatible with DB version |
| Access | DB user with schema privileges | Required |


---

## 4. Setup Guide

### 4.1 Install Java

```bash
sudo apt install openjdk-17-jdk -y
java -version
```
<img width="980" height="132" alt="Screenshot 2026-02-20 122702" src="https://github.com/user-attachments/assets/08a7b4f4-2ef5-4aa7-b88f-df33620dedba" />
<img width="553" height="53" alt="image" src="https://github.com/user-attachments/assets/7c43c22b-361d-4b44-8a88-db0cca05cbe4" />

---

### 4.2 Install PostgreSQL

```bash
sudo apt install postgresql postgresql-contrib -y
sudo systemctl start postgresql
```
<img width="1153" height="199" alt="Screenshot 2026-02-20 122728" src="https://github.com/user-attachments/assets/d0281fef-10eb-4ff2-a71d-14370aa19a10" />
<img width="1588" height="225" alt="Screenshot 2026-02-20 122743" src="https://github.com/user-attachments/assets/bdc198c5-4921-4ce5-938c-ca919c009c34" />


#### Create Database & User

```sql
CREATE DATABASE liquibase_poc;
CREATE USER liquibase_user WITH PASSWORD 'StrongPassword123';
GRANT ALL PRIVILEGES ON DATABASE liquibase_poc TO liquibase_user;
```
<img width="848" height="301" alt="image" src="https://github.com/user-attachments/assets/c2cb34de-77d4-4cd6-af10-a823faf1afcc" />

---

### 4.3 Install Liquibase

```bash
sudo apt update
sudo apt install liquibase -y
liquibase --version
```
<img width="1919" height="267" alt="Screenshot 2026-02-20 123045" src="https://github.com/user-attachments/assets/bbc9c15c-18b2-4287-b089-64926a587928" />
<img width="455" height="82" alt="Screenshot 2026-02-20 123116" src="https://github.com/user-attachments/assets/119faf4a-9e15-486d-b262-9b3db245c215" />

---

### 4.4 Install PostgreSQL JDBC Driver

```bash
sudo apt install libpostgresql-jdbc-java -y
```
<img width="1253" height="239" alt="Screenshot 2026-02-20 123422" src="https://github.com/user-attachments/assets/4fca24cf-f752-4f6a-ab0c-4f8effcec0ee" />

Verify:

```bash
ls /usr/share/java | grep postgresql
```
<img width="1084" height="135" alt="Screenshot 2026-02-20 123433" src="https://github.com/user-attachments/assets/c8d46107-07ab-445a-aa6d-0fe6c770c15d" />

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

```bash 
nano liquibase.properties
```

```properties
url=jdbc:postgresql://localhost:5432/liquibase_poc
username=liquibase_user
password=StrongPassword123
driver=org.postgresql.Driver
changeLogFile=db/changelog/db.changelog-master.xml
classpath=/usr/share/java/postgresql.jar
logLevel=info
```
<img width="751" height="235" alt="Screenshot 2026-02-20 123252" src="https://github.com/user-attachments/assets/c21ee921-403c-4880-b730-c6999505535c" />

---

### 4.7 Sample ChangeLog

```bash 
nano db/changelog/db.changelog-master.xml
```

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
<img width="1162" height="670" alt="Screenshot 2026-02-20 123331" src="https://github.com/user-attachments/assets/f662e19d-9b9b-4ceb-9d8c-a72502a60adf" />

---

## 5. Execution

### Apply Migration

```bash
liquibase update
```
<img width="1586" height="686" alt="Screenshot 2026-02-20 132755" src="https://github.com/user-attachments/assets/d259694e-0785-4380-9423-3a0551030242" />

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
<img width="1468" height="411" alt="Screenshot 2026-02-20 132829" src="https://github.com/user-attachments/assets/5fa8f341-9afc-4d92-8a3e-eb8ede3ac44a" />

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
<img width="1488" height="233" alt="image" src="https://github.com/user-attachments/assets/75f78fb4-53d9-4cc7-bd25-c7324a8dcbd0" />

This confirms Liquibase prevents duplicate execution.

---

## 7. Rollback Validation

```bash
liquibase rollback-count 1
```
<img width="1245" height="362" alt="image" src="https://github.com/user-attachments/assets/3219b7ae-53fa-494c-8a90-2b62c1cbc6a6" />

Verify:

```bash
\dt
```
<img width="1348" height="362" alt="Screenshot 2026-02-20 123537" src="https://github.com/user-attachments/assets/92240015-fe7f-415e-a8dd-f0ea4842cc9f" />

Result:

- users table removed
- metadata tables remain

---

## 8. Backup Strategy

Before production deployment:

```bash
pg_dump -U postgres liquibase_poc > backup.sql
```
<img width="810" height="68" alt="image" src="https://github.com/user-attachments/assets/0e824c68-6f2d-4cf0-a3cb-df31ed1f24a3" />

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

| Name            | Email                                   |
|-----------------|------------------------------------------|
| Ajitesh Singh  | ajitesh.singh.snaatak@mygurukulam.co     |

---

## 12. References

| Resource | Link |
|----------|------|
| Liquibase Documentation | https://docs.liquibase.com |
| PostgreSQL Documentation | https://www.postgresql.org/docs/ |
| Liquibase GitHub Repository | https://github.com/liquibase/liquibase |

