# Ansible Role CI Workflow

## Overview
This document describes the Continuous Integration (CI) workflow for Ansible roles.
The CI workflow ensures that every change to an Ansible role is automatically validated
for correctness, best practices, security, and reliability before being merged.

---

## CI Objectives
- Validate Ansible role syntax and structure
- Enforce Ansible best practices
- Detect errors early in the development lifecycle
- Ensure idempotent and repeatable role execution
- Improve overall code quality and reliability

---

## CI Workflow
1. Developer pushes code changes to the repository
2. CI pipeline is triggered automatically on commit or pull request
3. Validation and testing stages are executed
4. Pipeline fails if any stage fails
5. Pipeline passes if all stages succeed

---

## CI Pipeline Stages

### 1. YAML Syntax Validation
**Tool:** `yamllint`

- Validates YAML formatting and indentation
- Detects invalid syntax and duplicate keys

**Command:**
```bash
yamllint .
```
### 2. Ansible Playbook Syntax Check
**Command:**
```bash
ansible-playbook site.yml --syntax-check
```

- Validates playbook structure

- Checks task and variable syntax

### 3. Ansible Lint Check
**Tool: ansible-lint**

- Enforces Ansible best practices

- Detects deprecated modules and unsafe patterns

- Ensures consistent naming and style

**Command:**
```bash
ansible-lint .
```

### 4. Role Directory Structure Validation
**The following role structure is validated:**

```bash
tasks/main.yml

handlers/main.yml

defaults/main.yml

vars/main.yml

meta/main.yml
```

**Purpose: Ensures compliance with the standard Ansible role layout**

### 5. Idempotency Check
***The playbook is executed multiple times:***

```bash
ansible-playbook site.yml
ansible-playbook site.yml
```
- The second execution must report no changes

### 6. Molecule Testing (Recommended)
**Tool: Molecule**

- Executes the role in an isolated container or virtual machine

- Simulates real deployment environments

- Follows the create → converge → verify → destroy lifecycle

**Command:**
```bash
molecule test
```

### 7. Security and Secrets Validation
**Checks include:**

- Detection of hardcoded secrets

- Validation of Ansible Vault usage

- Basic secret scanning

### 8. Dry Run Validation (Check Mode)
**Command:**
```bash
ansible-playbook site.yml --check
```
- No changes are applied

- Displays the expected changes only


### CI Pipeline Flow

<img width="349" height="592" alt="image" src="https://github.com/user-attachments/assets/b70fc4b7-d843-4f54-8203-952ccca64711" />


### Tools Used
- Jenkins

- GitHub Actions

- GitLab CI

- Ansible

- Molecule

- yamllint

- ansible-lint

### CI Outcome
- Fully validated Ansible roles

- Early detection of syntax and logic errors

- Secure and consistent automation

- Production-ready configuration management
