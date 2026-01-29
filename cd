# Ansible Playbook – Continuous Deployment (CD) Workflow

| Author | Created On | Version | Last Updated By | Reviewer L0 | Reviewer L1 | Reviewer L2 |
|--------|------------|---------|------------------|-------------|-------------|-------------|
| Ajitesh Singh | 21-01-2026 | v1 | Ajitesh Singh | Priyanshu | Faisal | Mahesh |

---

##  Table of Contents
- [Introduction](#introduction)
- [Purpose](#purpose)
- [CD Workflow Overview](#cd-workflow-overview)
- [Architecture Flow](#architecture-flow)
- [Pre-requisites](#pre-requisites)
- [Directory Structure](#directory-structure)
- [Inventory Explanation](#inventory-explanation)
- [Playbook Breakdown](#playbook-breakdown)
- [Variables and Templates](#variables-and-templates)
- [Deployment Steps](#deployment-steps)
- [Rollback Strategy](#rollback-strategy)
- [Monitoring and Validation](#monitoring-and-validation)
- [Security Best Practices](#security-best-practices)
- [Troubleshooting](#troubleshooting)
- [Contact Information](#contact-information)
- [References](#references)

---

## Introduction
This document describes the **Continuous Deployment (CD) workflow using Ansible Playbooks**. It explains how application artifacts are automatically deployed to target servers using Ansible without manual intervention.

---

## Purpose
The purpose of this documentation is to standardize application deployment using automation, reduce manual errors, ensure consistent environments, and improve deployment reliability.

---

## Continuous Deployment (CD)

Continuous Deployment (CD) is the automated process of deploying a built application artifact to target servers without manual intervention.  
It ensures fast, consistent, and reliable releases by using automation tools like Ansible to deploy, validate, and maintain applications.





---

## Architecture Flow

| Component | Role |
|------------|------|
| Git Repository | Stores application source code |
| CI Tool | Builds artifacts |
| Artifact Repo | Stores build packages |
| Ansible Control Node | Executes playbooks |
| Managed Nodes | Target servers |
| Monitoring | Health validation |

---

## Pre-requisites

| Requirement | Description |
|--------------|-------------|
| Ansible | Installed on control node |
| SSH Access | Key-based authentication |
| Python | Required on managed nodes |
| Inventory File | Hosts list |
| Artifact Repo Access | Download permissions |

---

## CD Workflow Overview

<img width="387" height="656" alt="image" src="https://github.com/user-attachments/assets/cf49617d-e6fb-4e6a-ad66-5b3a9c4a6ed3" />

## Directory Structure

```
ansible-cd/
├── inventory.ini
├── deploy.yml
├── roles/
│   └── app-deploy/
│       ├── tasks/
│       ├── templates/
│       └── vars/
```

---

## Inventory Explanation

The inventory file defines the list of target servers where Ansible will execute the playbook.  
It organizes hosts into groups, allowing centralized and parallel management of multiple machines.

Example inventory:
```ini
[app_servers]
10.0.1.10
10.0.1.11
```

Defines target machines for deployment.

---

## Playbook Breakdown

Example playbook snippet:
```yaml
- name: Deploy Application
  hosts: app_servers
  become: yes
  roles:
    - app-deploy
```

Executes deployment tasks on target servers.

---

## Variables and Templates

Variables example:
```yaml
app_port: 8080
app_version: v1.0
```

Templates allow dynamic configuration using Jinja2.

---

## Deployment Steps

Run playbook:
```bash
ansible-playbook -i inventory.ini deploy.yml
```

Steps executed:
- Download artifact
- Extract files
- Update configuration
- Restart service
- Validate health

---

## Rollback Strategy

- Maintain previous artifact versions
- Restore last stable build
- Restart service
- Validate application

---

## Monitoring and Validation

Check service status:
```bash
systemctl status myapp
```

Health check:
```bash
curl http://localhost:8080/health
```

---

## Security Best Practices

- Use SSH key authentication
- Encrypt secrets with Ansible Vault
- Limit sudo privileges
- Restrict inventory access
- Rotate credentials

---

## Troubleshooting

| Issue | Cause | Solution |
|-------|--------|-----------|
| SSH failure | Key issue | Verify SSH keys |
| Service not starting | Config issue | Check logs |
| Timeout | Network issue | Verify connectivity |

---

## Contact Information

| Name | Email |
|------|-------|
| Ajitesh Singh | ajitesh.singh.snaatak@mygurukulam.co |

---

## References

| Link | Description |
|------|-------------|
| https://docs.ansible.com | Ansible Documentation |
| https://docs.ansible.com/ansible/latest/playbooks.html | Playbook Guide |
| https://www.redhat.com/en/topics/automation/what-is-ansible | Ansible Overview |

---
