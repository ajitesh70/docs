# POC – Static Code Analysis for React (Frontend) Using SonarQube

---

| Author | Created On | Version | Last Updated By | Reviewer L0 | Reviewer L1 | Reviewer L2 |
|--------|------------|---------|-----------------|-------------|-------------|-------------|
| Ajitesh Singh | 05-03-2026 | v1 | Ajitesh Singh | Priyanshu | Faisal | Mahesh |

---

## Table of Contents

- [Objective](#objective)
- [Environment Details](#environment-details)
- [Pre-requisites](#pre-requisites)
- [Architecture Flow](#architecture-flow)
- [SonarQube Installation](#sonarqube-installation)
- [Step 1 – Launch SonarQube Server](#step-1--launch-sonarqube-server)
- [Step 2 – Create Project in SonarQube](#step-2--create-project-in-sonarqube)
- [Step 3 – Clone React Repository](#step-3--clone-react-repository)
- [Step 4 – Install Node Dependencies](#step-4--install-node-dependencies)
- [Step 5 – Install SonarScanner](#step-5--install-sonarscanner)
- [Step 6 – Set SonarQube Token](#step-6--set-sonarqube-token)
- [Step 7 – Run Static Code Analysis](#step-7--run-static-code-analysis)
- [Step 8 – View Analysis Results](#step-8--view-analysis-results)
- [Step 9 – Quality Gate Evaluation](#step-9--quality-gate-evaluation)
- [Step 10 – Security Hotspot Review](#step-10--security-hotspot-review)
- [Result](#result)
- [Conclusion](#conclusion)
- [Contact Information](#contact-information)
- [References](#references)

---

## Objective

To demonstrate static code analysis on a React (frontend) application using SonarQube and SonarScanner CLI. The POC covers project scanning, quality gate evaluation, and security hotspot review as part of a CI quality check.

---

## Environment Details

| Component | Details |
|---|---|
| Application | Frontend (React) |
| OS | Ubuntu (EC2 Instance) |
| Node.js | v16+ |
| SonarQube | Community Edition (running on EC2) |
| SonarScanner | CLI v5.0.1 |
| EC2 Public IP | 13.61.27.0 |

---

## Pre-requisites

| Requirement | Details |
|---|---|
| SonarQube | Installed and running on EC2 |
| Node.js & npm | Installed on EC2 |
| Git | Installed and configured |
| Internet Access | Required for cloning repo and downloading scanner |

---

## Architecture Flow

```
React Source Code
        |
        v
SonarScanner CLI
        |
        v
SonarQube Server
        |
        v
Static Code Analysis Report
        |
        v
Quality Gate Evaluation
```

---

## SonarQube Installation

If SonarQube is not yet installed on your EC2 instance, follow the installation guide before proceeding:

| Resource | Link |
|---|---|
| SonarQube Installation Guide | https://docs.sonarsource.com/sonarqube/latest/setup-and-upgrade/install-the-server/introduction/ |
| Ansible Role POC – SonarQube Setup | ./POC-sonarqube-ansible-role.md |

---

## Step 1 – Launch SonarQube Server

Start SonarQube on the EC2 instance:

```bash
sudo systemctl start sonarqube
```

Verify the service is running:

```bash
sudo systemctl status sonarqube
```

<!-- Attach screenshot: systemctl status output showing active/running -->
![SonarQube Status](./screenshots/sonarqube-status.png)

Access the dashboard in your browser:

```
http://13.61.27.0:9000
```

Log in with default credentials: `admin / admin`

<!-- Attach screenshot: SonarQube login / dashboard page -->
![SonarQube Dashboard](./screenshots/sonarqube-dashboard.png)

---

## Step 2 – Create Project in SonarQube

Inside the SonarQube UI navigate to:

```
Projects → Create Project → Manually
```

Provide the following details:

| Field | Value |
|---|---|
| Project Key | frontend |
| Project Name | frontend |

Click **Set Up** and generate a project token. Copy and save the token — it will be used in Step 6.

<!-- Attach screenshot: Project creation page with key and name filled -->
![Create Project](./screenshots/create-project.png)

<!-- Attach screenshot: Token generation page -->
![Generate Token](./screenshots/generate-token.png)

---

## Step 3 – Clone React Repository

On the EC2 instance clone the frontend project:

```bash
git clone https://github.com/OT-MICROSERVICES/frontend.git
cd frontend
```

Verify the project structure:

```bash
ls
```

Expected output:

```
src  package.json  public  Dockerfile
```

<!-- Attach screenshot: ls output showing project structure -->
![Project Structure](./screenshots/project-structure.png)

---

## Step 4 – Install Node Dependencies

Install all React project dependencies:

```bash
npm install
```

This downloads and installs all libraries listed in `package.json`.

<!-- Attach screenshot: npm install output -->
![NPM Install](./screenshots/npm-install.png)

---

## Step 5 – Install SonarScanner

Download and extract SonarScanner CLI:

```bash
cd /opt
sudo wget https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-5.0.1.3006-linux.zip
sudo unzip sonar-scanner-cli-5.0.1.3006-linux.zip
```

Add SonarScanner to PATH:

```bash
export PATH=$PATH:/opt/sonar-scanner-5.0.1.3006-linux/bin
```

Verify the installation:

```bash
sonar-scanner -v
```

Expected output:

```
SonarScanner 5.0.1.3006
```

<!-- Attach screenshot: sonar-scanner -v output -->
![SonarScanner Version](./screenshots/sonarscanner-version.png)

---

## Step 6 – Set SonarQube Token

Export the token generated from SonarQube in Step 2:

```bash
export SONAR_TOKEN=<your_project_token>
```

---

## Step 7 – Run Static Code Analysis

Navigate back to the React project directory:

```bash
cd ~/frontend
```

Run the SonarScanner:

```bash
sonar-scanner \
  -Dsonar.projectKey=frontend \
  -Dsonar.sources=src \
  -Dsonar.host.url=http://localhost:9000 \
  -Dsonar.login=$SONAR_TOKEN
```

| Parameter | Purpose |
|---|---|
| `sonar.projectKey` | Matches the project created in SonarQube |
| `sonar.sources` | Points scanner to the React source directory |
| `sonar.host.url` | SonarQube server address |
| `sonar.login` | Authentication token |

<!-- Attach screenshot: sonar-scanner CLI running / completion output -->
![Sonar Scanner Run](./screenshots/sonar-scanner-run.png)

---

## Step 8 – View Analysis Results

Open the SonarQube dashboard:

```
http://13.61.27.0:9000
```

Navigate to:

```
Projects → frontend
```

Review the following metrics:

| Metric | Description |
|---|---|
| Bugs | Confirmed runtime errors |
| Vulnerabilities | Exploitable security weaknesses |
| Code Smells | Maintainability issues |
| Security Hotspots | Risky patterns requiring manual review |
| Maintainability Rating | A–E grade based on debt ratio |
| Reliability Rating | A–E grade based on bug severity |

<!-- Attach screenshot: SonarQube project dashboard showing all metrics -->
![Analysis Results](./screenshots/analysis-results.png)

---

## Step 9 – Quality Gate Evaluation

SonarQube automatically evaluates the project against defined quality rules.

<!-- Attach screenshot: Quality Gate status (Passed/Failed) -->
![Quality Gate](./screenshots/quality-gate.png)

Expected result:

| Metric | Result |
|---|---|
| Quality Gate | Passed |
| Reliability Rating | A |
| Security Rating | A |
| Maintainability Rating | A |

---

## Step 10 – Security Hotspot Review

Navigate to **Security Hotspots** in the SonarQube dashboard and review each flagged item. Mark each as:

| Status | Meaning |
|---|---|
| Reviewed | Hotspot has been manually assessed |
| Safe | Risk accepted — no action required |
| Fixed | Code has been corrected |

<!-- Attach screenshot: Security Hotspots list in SonarQube -->
![Security Hotspots](./screenshots/security-hotspots.png)

---

## Result

| Outcome | Status |
|---|---|
| SonarQube server running on EC2 | Success |
| React project scanned via SonarScanner CLI | Success |
| Analysis results visible on dashboard | Success |
| Quality Gate evaluated | Success |
| Security Hotspots reviewed | Success |

---

## Conclusion

This POC successfully demonstrates static code analysis for a React frontend application using SonarQube and SonarScanner CLI. The analysis provides visibility into bugs, vulnerabilities, code smells, and security hotspots before deployment — making it a reliable CI quality gate for frontend projects.

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
| SonarScanner CLI | https://docs.sonarsource.com/sonarqube/latest/analyzing-source-code/scanners/sonarscanner/ |
| Frontend Repository | https://github.com/OT-MICROSERVICES/frontend |
| SonarQube Quality Gates | https://docs.sonarsource.com/sonarqube/latest/user-guide/quality-gates/ |
