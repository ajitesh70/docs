# 🚀 Setup DevOps Repositories

**Epic:** VCS Implementation  
**Task:** Setup Repos  
**Activity:** Setup DevOps Repos (Sprint Recommendation Implementation)  

| Author        | Created On | Version | Last Updated By | Last Edited On | L0 Reviewer   | L1 Reviewer   | L2 Reviewer   |
|---------------|------------|---------|-----------------|----------------|---------------|---------------|---------------|
| Shobhit Goyal | 23-02-2026 | v3.0    | Shobhit Goyal   | 23-02-2026     | Komal Jaiswal | Akshit Kapil  | Mahesh Kumar  |

---

## 📑 Table of Contents
1. [Introduction](#introduction)  
2. [Objective](#objective)  
3. [Repository Strategy Decision](#repository-strategy-decision)  
4. [DevOps Microrepo Structure](#devops-microrepo-structure)  
5. [Branch Strategy](#branch-strategy)  
6. [Branch Protection Rules](#branch-protection-rules)  
7. [Conclusion](#conclusion)  
8. [Contact Information](#contact-information)  

---

## 1️⃣ Introduction
As recommended in the last sprint, DevOps repositories have been designed and implemented under the **VCS Implementation → Setup Repos** task.

The repositories are structured using an **Epic-based Microrepo model** on GitHub.

---

## 2️⃣ Objective
The objective of **Setup DevOps Repos** is to:
- Implement structured repository architecture  
- Align repositories with DevOps Epics  
- Enable scalable CI/CD  
- Enforce governance through branch protection  
- Prepare for enterprise-grade DevOps workflow  

---

## 3️⃣ Repository Strategy Decision
Two approaches were evaluated:

### 🔹 Monorepo
Single repository containing all DevOps components.

### 🔹 Microrepo
Separate repositories for each Epic.

**✅ Selected: Microrepo**

**Reason:**
- Clear ownership per Epic  
- Independent CI/CD pipelines  
- Easier scaling  
- Technology isolation  
- Better access control  

---

## 4️⃣ DevOps Microrepo Structure
Organization contains the following **Epic-based repositories**:

```bash
devops-organization/
├── vcs-implementation/
├── application-ci-design/
├── cloud-infra-design/
├── cost-optimization/
└── ansible-cicd/
```
<img width="1265" height="587" alt="image" src="https://github.com/user-attachments/assets/209b6f27-074d-4be8-9a45-ac911a92fc4b" />



### 🔹 4.1 VCS Implementation

```bash
vcs-implementation/
├── setup-vcs/
├── setup-repos/
├── setup-authentication/
├── setup-authorization/
├── setup-workflow/
├── setup-notification/
└── setup-commithooks/
```

This repository directly implements:  
- Epic → VCS Implementation  
- Task → Setup Repos  
- Deliverable → DevOps Repositories Structured  

### 🔹 4.2 Application CI Design
```bash
application-ci-design/
├── understanding/
├── generic-ci-operation/
├── react-ci-checks/
├── java-ci-checks/
├── python-ci-checks/
├── golang-ci-checks/
├── ci-orchestration-tools/
├── sonarqube/
└── jenkins/
```

**CI orchestration tools integrated:**
- Jenkins  
- SonarQube  

### 🔹 4.3 Cloud Infra Design
```bash
cloud-infra-design/
├── cloud-infra-design-30k-feet/
└── cloud-infra-design-dev/
```

### 🔹 4.4 Cost Optimization

```bash
cost-optimization/
└── implementation/
```

### 🔹 4.5 Ansible CI/CD

```bash
ansible-cicd/
├── ansible-role-cicd/
└── ansible-playbook-cicd/
```

---

## 5️⃣ Branch Strategy
Standardized across all repositories:

- `main`  
- `develop`  
- `feature-XXX`  

**Workflow:**
1. Create `feature-task-name`  
2. Raise PR → `develop`  
3. CI validation  
4. Merge `develop` → `main`  

---

## 6️⃣ Branch Protection Rules

Navigation:  
**Repository → Settings → Branches → Add Rule**

Applied to `main`:
- ✅ Require Pull Request  
- ✅ Require reviewer approval  
- ✅ Require CI checks to pass  
- ✅ Block direct push  
- ✅ Enforce up-to-date branches  

---

## ✅ Conclusion
The DevOps repositories have been successfully implemented as per sprint recommendation under:

- Epic → VCS Implementation  
- Task → Setup Repos  
- Deliverable → Structured DevOps Microrepo Architecture  

This setup ensures:
- ✔ Governance  
- ✔ CI/CD readiness  
- ✔ Cloud alignment  
- ✔ Enterprise DevOps scalability  

---

## 📬 Contact Information
| Name          | Email                                    |
|---------------|------------------------------------------|
| Shobhit Goyal | shobhit.goyal.snaatak@gmygurukulam.co    |
