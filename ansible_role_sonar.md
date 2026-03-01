# Proof of Concept (POC)
## Software Configuration – Ansible Role to Setup SonarQube

---

# 1. Objective

To automate the installation and configuration of SonarQube using Ansible role-based structure on an Ubuntu 22.04 server.

The POC demonstrates Infrastructure as Code (IaC) principles and configuration management automation.

---

# 2. Environment Details

- OS: Ubuntu 22.04
- Instance Type: t3.medium (4GB RAM recommended)
- Automation Tool: Ansible
- Application: SonarQube 10.3
- Database: PostgreSQL (local)

---

# 3. Architecture (POC Level)

Single Node Deployment:

EC2 Server
 ├── OpenJDK 17
 ├── PostgreSQL
 ├── SonarQube
 └── systemd Service

---

# 4. Project Structure

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

---

# 5. Implementation Steps

## Step 1: Create Ansible Role

ansible-galaxy init sonarqube

This creates the modular role structure.

---

## Step 2: Configure Inventory

inventory.ini:

[sonarqube]
<EC2_PUBLIC_IP> ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/<keyfile>

---

## Step 3: Configure Playbook

playbook.yml:

---
- name: Install SonarQube
  hosts: sonarqube
  become: yes
  roles:
    - sonarqube

---

# 6. Configuration Changes Implemented

## 6.1 Installed Required Packages

- openjdk-17-jdk
- postgresql
- python3-psycopg2
- unzip
- wget

Purpose:
Ensure all runtime and database dependencies are present.

---

## 6.2 PostgreSQL Configuration

- Started PostgreSQL service
- Enabled at boot
- Created database: sonarqube
- Created user: sonar
- Granted database privileges

Purpose:
SonarQube requires external PostgreSQL backend.

---

## 6.3 SonarQube Installation

- Downloaded SonarQube binary
- Extracted to /opt
- Renamed directory to /opt/sonarqube
- Created dedicated system user: sonar
- Set directory ownership to sonar

Purpose:
Ensure secure execution under non-root user.

---

## 6.4 SonarQube Configuration

Updated sonar.properties with:

sonar.jdbc.username=sonar
sonar.jdbc.password=sonar
sonar.jdbc.url=jdbc:postgresql://localhost/sonarqube

Purpose:
Connect SonarQube to PostgreSQL database.

---

## 6.5 Created systemd Service

Created:

/etc/systemd/system/sonarqube.service

Configured:

- Runs as sonar user
- Auto restart enabled
- File descriptor limits defined
- Enabled at boot

Purpose:
Ensure proper service management and restart capability.

---

# 7. Execution

## Test Connectivity

ansible -i inventory.ini sonarqube -m ping

Expected:
SUCCESS => pong

## Run Playbook

ansible-playbook -i inventory.ini playbook.yml

---

# 8. Verification

Check service:

sudo systemctl status sonarqube

Check listening port:

sudo ss -tulnp | grep 9000

Access web interface:

http://<EC2_PUBLIC_IP>:9000

Default login:
Username: admin
Password: admin

---

# 9. Challenges Faced

- SSH broken pipe due to network instability
- Need for minimum 4GB RAM
- Public IP changes after instance restart
- PostgreSQL permission handling

---

# 10. Limitations of This POC

- Database deployed on same server
- No SSL configuration
- No reverse proxy
- No Ansible Vault for secrets
- No kernel parameter tuning for Elasticsearch

---

# 11. Future Improvements

- Separate PostgreSQL server
- Add Nginx reverse proxy
- Configure HTTPS
- Use PostgreSQL Ansible modules
- Parameterize version in defaults
- Implement backup strategy

---

# 12. Conclusion

This POC successfully demonstrates:

- Role-based configuration management
- Automated installation of SonarQube
- PostgreSQL provisioning
- Service configuration using systemd
- Infrastructure as Code principles

The solution is modular, repeatable, and extendable to production environments.
