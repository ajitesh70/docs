# Proof of Concept (POC) – Ansible Role to Setup SonarQube

---

| Author | Created On | Version | Last Updated By | Reviewer L0 | Reviewer L1 | Reviewer L2 |
|--------|------------|---------|-----------------|-------------|-------------|-------------|
| Ajitesh Singh | 05-01-2026 | v1 | Ajitesh Singh | Priyanshu | Faisal | Mahesh |

---

## Table of Contents

- [Objective](#objective)
- [Environment Details](#environment-details)
- [Architecture](#architecture)
- [Project Structure](#project-structure)
- [Implementation Steps](#implementation-steps)
- [Configuration Changes Implemented](#configuration-changes-implemented)
- [Execution](#execution)
- [Verification](#verification)
- [Challenges Faced](#challenges-faced)
- [Limitations](#limitations)
- [Future Improvements](#future-improvements)
- [Conclusion](#conclusion)
- [Contact Information](#contact-information)
- [References](#references)

---

## Objective

To automate the installation and configuration of SonarQube using an Ansible role-based structure on an Ubuntu 22.04 server. This POC demonstrates Infrastructure as Code (IaC) principles and configuration management automation.

---

## Environment Details

| Component | Details |
|---|---|
| OS | Ubuntu 22.04 |
| Instance Type | t3.medium (4GB RAM recommended) |
| Automation Tool | Ansible |
| Application | SonarQube 10.3 |
| Database | PostgreSQL (local) |

---

## Architecture

Single Node Deployment (POC Level):

```
EC2 Server
 ├── OpenJDK 17
 ├── PostgreSQL
 ├── SonarQube
 └── systemd Service
```

---

## Project Structure

```
sonar-ansible/
├── inventory.ini
├── playbook.yml
└── sonarqube/
    ├── defaults/
    ├── handlers/
    ├── meta/
    ├── tasks/
    ├── templates/
    └── vars/
```

---

## Implementation Steps

### Step 1 – Create Ansible Role

```bash
ansible-galaxy init sonarqube
```
<img width="911" height="54" alt="Screenshot 2026-02-28 194239" src="https://github.com/user-attachments/assets/e052d85f-e80d-467e-bec6-d268b932b4fb" />

This creates the modular role structure.

### Step 2 – Configure Inventory

**File:** `inventory.ini`

```ini
[sonarqube]
<EC2_PUBLIC_IP> ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/<keyfile>
```
<img width="865" height="55" alt="image" src="https://github.com/user-attachments/assets/1e23d1ee-c297-474f-9f19-ccb33986b78c" />


### Step 3 – Configure Playbook

**File:** `playbook.yml`

```yaml
- name: Install SonarQube
  hosts: sonarqube
  become: yes
  roles:
    - sonarqube
```
<img width="571" height="158" alt="image" src="https://github.com/user-attachments/assets/c0a408c7-8402-408e-82fc-50467f523672" />

---

## Configuration Changes Implemented

### 6.1 Installed Required Packages

- `openjdk-17-jdk`
- `postgresql`
- `python3-psycopg2`
- `unzip`
- `wget`

**Purpose:** Ensure all runtime and database dependencies are present.

<img width="416" height="206" alt="image" src="https://github.com/user-attachments/assets/2969b794-2804-4f17-ac4d-15b87410c264" />

---

### 6.2 PostgreSQL Configuration

- Started PostgreSQL service and enabled at boot
- Created database: `sonarqube`
- Created user: `sonar`
- Granted database privileges

**Purpose:** SonarQube requires an external PostgreSQL backend.

<img width="450" height="138" alt="image" src="https://github.com/user-attachments/assets/a2d03e69-5ffc-46a1-886d-12e17ad2b903" />

<img width="1882" height="394" alt="image" src="https://github.com/user-attachments/assets/afe5f4cc-a3dc-4d08-b0ee-1a15d707e886" />

---

### 6.3 SonarQube Installation

- Downloaded SonarQube binary
- Extracted to `/opt`
- Renamed directory to `/opt/sonarqube`
- Created dedicated system user: `sonar`
- Set directory ownership to `sonar`

**Purpose:** Ensure secure execution under a non-root user.
<img width="1033" height="420" alt="image" src="https://github.com/user-attachments/assets/3c9724f9-b453-499d-bff6-3e5d0aef8305" />
<img width="578" height="358" alt="image" src="https://github.com/user-attachments/assets/2bec548e-5611-4347-8c75-fa78cf99d356" />


---

### 6.4 SonarQube Configuration

Updated `sonar.properties` with:

```properties
sonar.jdbc.username=sonar
sonar.jdbc.password=sonar
sonar.jdbc.url=jdbc:postgresql://localhost/sonarqube
```
<img width="1118" height="296" alt="image" src="https://github.com/user-attachments/assets/c3757a6f-7237-4156-be3a-c460915f63d7" />

**Purpose:** Connect SonarQube to the PostgreSQL database.

---

### 6.5 Created systemd Service

**File:** `/etc/systemd/system/sonarqube.service`

Configured with:

- Runs as `sonar` user
- Auto restart enabled
- File descriptor limits defined
- Enabled at boot

**Purpose:** Ensure proper service management and restart capability.
<img width="740" height="866" alt="image" src="https://github.com/user-attachments/assets/e2af6193-522d-4cbe-92ab-bb09903d0ad5" />

---

## Execution

### Test Connectivity

```bash
ansible -i inventory.ini sonarqube -m ping
```
<img width="1061" height="278" alt="Screenshot 2026-03-01 182429" src="https://github.com/user-attachments/assets/e25aa0f5-5cc1-4327-9529-b642f2036fcb" />

Expected output:

```
<EC2_PUBLIC_IP> | SUCCESS => {
    "ping": "pong"
}
```

### Run Playbook

```bash
ansible-playbook -i inventory.ini playbook.yml
```
<img width="1190" height="176" alt="image" src="https://github.com/user-attachments/assets/01ab8269-cd6f-401b-90b4-d66da9bef08e" />


---

## Verification

**Check service status:**

```bash
sudo systemctl status sonarqube
```
<img width="1051" height="260" alt="image" src="https://github.com/user-attachments/assets/13742fa1-1b75-4e0a-a755-b5af7360de3e" />

**Check listening port:**

```bash
sudo ss -tulnp | grep 9000
```
<img width="1177" height="89" alt="image" src="https://github.com/user-attachments/assets/8d9ec275-8653-41ee-9d73-5dbbb167622a" />


**Access web interface:**

```
http://<EC2_PUBLIC_IP>:9000
```
<img width="1494" height="399" alt="Screenshot 2026-03-02 125630" src="https://github.com/user-attachments/assets/023405ca-a9a5-4ebe-ba91-01c28b8460f3" />

<img width="597" height="608" alt="image" src="https://github.com/user-attachments/assets/c45da902-1168-4493-90fb-4202fbe4d247" />


| Field | Value |
|---|---|
| Username | admin |
| Password | admin |

> Change default credentials immediately after first login.
<img width="1811" height="603" alt="image" src="https://github.com/user-attachments/assets/8f5677e5-3e7a-4d3f-9ec1-13e14ce23db1" />

---

## Challenges Faced

- SSH broken pipe due to network instability
- Minimum 4GB RAM required for SonarQube to start
- Public IP changes after instance restart
- PostgreSQL permission handling

---

## Limitations

- Database deployed on the same server
- No SSL configuration
- No reverse proxy
- No Ansible Vault for secrets
- No kernel parameter tuning for Elasticsearch

---

## Future Improvements

- Separate PostgreSQL server
- Add Nginx reverse proxy
- Configure HTTPS
- Use PostgreSQL Ansible modules
- Parameterize version in defaults
- Implement backup strategy

---

## Conclusion

This POC successfully demonstrates:

- Role-based configuration management
- Automated installation of SonarQube
- PostgreSQL provisioning
- Service configuration using systemd
- Infrastructure as Code principles

The solution is modular, repeatable, and extendable to production environments.

---

## Contact Information

| Name | Email |
|------|-------|
| Ajitesh Singh | ajitesh.singh.snaatak@mygurukulam.co |

---

## References

| Title | Link |
|---|---|
| SonarQube Official Documentation | https://docs.sonarsource.com/sonarqube/latest/ |
| Ansible Documentation | https://docs.ansible.com/ |
| community.postgresql Collection | https://docs.ansible.com/ansible/latest/collections/community/postgresql/ |
| SonarQube System Requirements | https://docs.sonarsource.com/sonarqube/latest/requirements/requirements/ |
