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

This creates the modular role structure.

### Step 2 – Configure Inventory

**File:** `inventory.ini`

```ini
[sonarqube]
<EC2_PUBLIC_IP> ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/<keyfile>
```

### Step 3 – Configure Playbook

**File:** `playbook.yml`

```yaml
- name: Install SonarQube
  hosts: sonarqube
  become: yes
  roles:
    - sonarqube
```

---

## Configuration Changes Implemented

### 6.1 Installed Required Packages

- `openjdk-17-jdk`
- `postgresql`
- `python3-psycopg2`
- `unzip`
- `wget`

**Purpose:** Ensure all runtime and database dependencies are present.

---

### 6.2 PostgreSQL Configuration

- Started PostgreSQL service and enabled at boot
- Created database: `sonarqube`
- Created user: `sonar`
- Granted database privileges

**Purpose:** SonarQube requires an external PostgreSQL backend.

---

### 6.3 SonarQube Installation

- Downloaded SonarQube binary
- Extracted to `/opt`
- Renamed directory to `/opt/sonarqube`
- Created dedicated system user: `sonar`
- Set directory ownership to `sonar`

**Purpose:** Ensure secure execution under a non-root user.

---

### 6.4 SonarQube Configuration

Updated `sonar.properties` with:

```properties
sonar.jdbc.username=sonar
sonar.jdbc.password=sonar
sonar.jdbc.url=jdbc:postgresql://localhost/sonarqube
```

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

---

## Execution

### Test Connectivity

```bash
ansible -i inventory.ini sonarqube -m ping
```

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

---

## Verification

**Check service status:**

```bash
sudo systemctl status sonarqube
```

**Check listening port:**

```bash
sudo ss -tulnp | grep 9000
```

**Access web interface:**

```
http://<EC2_PUBLIC_IP>:9000
```

| Field | Value |
|---|---|
| Username | admin |
| Password | admin |

> Change default credentials immediately after first login.

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
