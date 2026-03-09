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

<img width="1541" height="237" alt="Screenshot 2026-03-09 133033" src="https://github.com/user-attachments/assets/60e06338-78f3-41da-9859-791cfc783f8c" />


Access the dashboard in your browser:

```
http://13.61.27.0:9000
```

Log in with default credentials: `admin / admin`

<img width="634" height="340" alt="image" src="https://github.com/user-attachments/assets/76511a15-b39d-4621-a11a-e18986824685" />


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

<img width="708" height="462" alt="image" src="https://github.com/user-attachments/assets/9d4701fe-87f0-461a-b047-5163d3c9f10b" />

<img width="461" height="371" alt="image" src="https://github.com/user-attachments/assets/90e99231-ebb1-4523-a5f7-0faa610e5f5e" />

<img width="958" height="438" alt="image" src="https://github.com/user-attachments/assets/2e8c3bd1-490d-4add-b998-ac3c980e0fe4" />


---

## Step 3 – Clone React Repository

On the EC2 instance clone the frontend project:

```bash
git clone https://github.com/OT-MICROSERVICES/frontend.git
cd frontend
```
<img width="1216" height="129" alt="Screenshot 2026-03-09 132910" src="https://github.com/user-attachments/assets/6e82afc4-30b7-4b88-af74-f6a35f9b667e" />

Verify the project structure:

```bash
ls
```

Expected output:

```
src  package.json  public  Dockerfile
```

<img width="1101" height="75" alt="Screenshot 2026-03-09 132941" src="https://github.com/user-attachments/assets/11bbc6a9-c9bf-4953-ad33-65de0b872cb4" />


---

## Step 4 – Install Node Dependencies

Install all React project dependencies:

```bash
npm install
```

This downloads and installs all libraries listed in `package.json`.

<img width="960" height="138" alt="Screenshot 2026-03-09 133014" src="https://github.com/user-attachments/assets/57bb523a-ea9c-4f57-9227-4e1e9b395050" />

<img width="1140" height="363" alt="image" src="https://github.com/user-attachments/assets/d7d539bf-76f2-4bdf-88c7-386e94ec8d4f" />


---

## Step 5 – Install SonarScanner

Download and extract SonarScanner CLI:

```bash
cd /opt
sudo wget https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-5.0.1.3006-linux.zip
sudo unzip sonar-scanner-cli-5.0.1.3006-linux.zip
```
<img width="1837" height="329" alt="Screenshot 2026-03-09 133124" src="https://github.com/user-attachments/assets/4b57a3fc-7390-49c0-8a83-a47e830cd24d" />
<img width="1001" height="278" alt="Screenshot 2026-03-09 133135" src="https://github.com/user-attachments/assets/78430ede-185a-496d-bac1-9f18ecc81583" />


Add SonarScanner to PATH:

```bash
export PATH=$PATH:/opt/sonar-scanner-5.0.1.3006-linux/bin
```
<img width="1354" height="35" alt="image" src="https://github.com/user-attachments/assets/7e0c7010-5413-4ef1-ae76-10bb8b31323d" />

Verify the installation:

```bash
sonar-scanner -v
```


Expected output:

```
SonarScanner 5.0.1.3006
```

<img width="1541" height="205" alt="image" src="https://github.com/user-attachments/assets/2bdb6746-c38d-426e-a4af-68bd6a3bf243" />

---

## Step 6 – Set SonarQube Token

Export the token generated from SonarQube in Step 2:

```bash
export SONAR_TOKEN=<your_project_token>
```
<img width="1349" height="32" alt="image" src="https://github.com/user-attachments/assets/56e3f525-dfb4-4656-a8fe-17edbb7ba619" />

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

<img width="735" height="176" alt="image" src="https://github.com/user-attachments/assets/80761fde-b377-41e2-8d0c-4cd425d251ad" />


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



---

## Step 9 – Quality Gate Evaluation

SonarQube automatically evaluates the project against defined quality rules.

<img width="1065" height="539" alt="image" src="https://github.com/user-attachments/assets/222b172b-24f2-4006-9b26-993efddc9eb5" />

<img width="1043" height="342" alt="image" src="https://github.com/user-attachments/assets/f3f4e242-8836-4f8b-a4f7-301d9284f12d" />

Expected result:

| Metric | Result |
|---|---|
| Quality Gate | Passed |
| Reliability Rating | A |
| Security Rating | E |
| Maintainability Rating | A |

---

## Step 10 – Security Hotspot Review

Navigate to **Security Hotspots** in the SonarQube dashboard. Review and mark each as **Safe**, **Fixed**, or **Acknowledged**.

**Hotspots detected: 4**

| Priority | Issue |
|---|---|
| Medium | `COPY . /app` — recursive copy may expose sensitive data |
| Medium | Node image runs as root user |
| Low | 2 additional items under Others |

<img width="1458" height="379" alt="image" src="https://github.com/user-attachments/assets/a385c7c8-a449-4a0c-8a76-3ce81fa676ba" />


---

## Step 11 – Code Smell Review

Navigate to **Issues → Code Smells** in the SonarQube dashboard.

**Code smells detected: 2 issues · 10 min effort · File: Dockerfile**

| Type | Issue | Severity | Effort |
|---|---|---|---|
| Consistency | Replace deprecated instructions with an up-to-date equivalent | Major | 5 min |
| Intentionality | Remove cache after installing packages | Major | 5 min |

Both are Maintainability issues flagged in the `Dockerfile` and should be resolved in the next sprint.

<img width="1451" height="443" alt="image" src="https://github.com/user-attachments/assets/9ceb3917-fa13-4662-b8a9-0e843f6e403d" />


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
