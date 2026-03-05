# Proof of Concept (POC) – Bug Analysis Using SonarQube

---

| Author | Created On | Version | Last Updated By | Reviewer L0 | Reviewer L1 | Reviewer L2 |
|--------|------------|---------|-----------------|-------------|-------------|-------------|
| Ajitesh Singh | 05-01-2026 | v1 | Ajitesh Singh | Priyanshu | Faisal | Mahesh |

---

## Table of Contents

- [Objective](#objective)
- [Environment Setup](#environment-setup)
- [Pre-requisites](#pre-requisites)
- [Step 1 – Clone the Repository](#step-1--clone-the-repository)
- [SonarQube Installation](#sonarqube-installation)
- [Step 2 – Start SonarQube](#step-2--start-sonarqube)
- [Step 3 – Generate SonarQube Token](#step-3--generate-sonarqube-token)
- [Step 4 – Run Baseline Analysis](#step-4--run-baseline-analysis)
- [Step 5 – Introducing a Controlled Bug](#step-5--introducing-a-controlled-bug)
- [Step 6 – Running Static Analysis](#step-6--running-static-analysis)
- [Step 7 – Bug Detection by SonarQube](#step-7--bug-detection-by-sonarqube)
- [Step 8 – Root Cause Analysis](#step-8--root-cause-analysis)
- [Step 9 – Bug Fix Implementation](#step-9--bug-fix-implementation)
- [Step 10 – Re-Analysis](#step-10--re-analysis)
- [Coverage Impact](#coverage-impact)
- [Bug Lifecycle](#bug-lifecycle)
- [Observations](#observations)
- [Conclusion](#conclusion)
- [Contact Information](#contact-information)
- [References](#references)

---

## Objective

To demonstrate how SonarQube detects runtime reliability defects through static analysis and classifies them by severity. This POC covers the complete bug lifecycle — from detection and root cause analysis to fix implementation and re-analysis verification.

---

## Environment Setup

| Component | Details |
|---|---|
| Project | salary-api |
| Language | Java 17 |
| Framework | Spring Boot |
| Build Tool | Maven |
| Static Analysis Tool | SonarQube Community Edition |
| Coverage Tool | JaCoCo |
| Sonar Database | H2 (Embedded) |
| OS | Linux |

---

## Pre-requisites

| Requirement | Details |
|---|---|
| Java | OpenJDK 17 installed |
| Git | Installed and configured |
| SonarQube | Running on `http://localhost:9000` |
| Maven Wrapper | Included in the project (`mvnw`) |
| Internet Access | Required for dependency download on first build |

---

## Step 1 – Clone the Repository

Clone the `salary-api` project to the local machine:

```bash
git clone https://github.com/<your-org>/salary-api.git
cd salary-api
```
<img width="1074" height="208" alt="image" src="https://github.com/user-attachments/assets/b1a76853-468b-479a-84e4-ed232073d6a2" />


Verify the project structure:

```bash
ls
```

Expected output:

```
mvnw  mvnw.cmd  pom.xml  src/  target/
```
<img width="1281" height="67" alt="image" src="https://github.com/user-attachments/assets/628f0747-ff97-447d-812e-cd716870f487" />

Provide execute permission to the Maven Wrapper:

```bash
chmod +x mvnw
```
<img width="752" height="33" alt="Screenshot 2026-03-04 210028" src="https://github.com/user-attachments/assets/3cc934ad-fbce-45b9-a238-1624d95b4230" />

---

## SonarQube Installation

If SonarQube is not installed on your system, follow the official installation guide before proceeding:

| Resource | Link |
|---|---|
| SonarQube Installation Guide | https://docs.sonarsource.com/sonarqube/latest/setup-and-upgrade/install-the-server/introduction/ |
| Ansible Role POC – SonarQube Setup | [SonarQube Installation Role - GitHub Repository](https://github.com/ajitesh70/sonarqube_installation_role)|


---

---

## Step 2 – Start SonarQube

If SonarQube is not already running, start it:

```bash
cd /opt/sonarqube/bin/linux-x86-64
./sonar.sh start
```

Verify SonarQube is accessible at `http://localhost:9000`

Default credentials: `admin / admin`

---

## Step 3 – Generate SonarQube Token

1. Log in to SonarQube at `http://localhost:9000`
2. Navigate to **My Account → Security → Generate Token**
3. Copy the token and export it:

```bash
export SONAR_TOKEN=<your-token-here>
```

---

## Step 4 – Run Baseline Analysis

Before introducing any bug, run a baseline analysis to confirm the project is in a clean state:

```bash
./mvnw clean verify -Dtest=\!SalaryApplicationTests sonar:sonar \
  -Dsonar.projectKey=salary-api \
  -Dsonar.host.url=http://localhost:9000 \
  -Dsonar.login=$SONAR_TOKEN
```

**Initial project state (before introducing bug):**

| Metric | Value |
|---|---|
| Bugs | 0 |
| Reliability Rating | A |
| Quality Gate | Passed |

<img width="1130" height="519" alt="image" src="https://github.com/user-attachments/assets/619a133b-421b-4b17-a70d-c35cd2a2442e" />
<img width="333" height="144" alt="image" src="https://github.com/user-attachments/assets/97f5c7a3-5e46-46a2-8185-ea2610584118" />

---

## Step 5 – Introducing a Controlled Bug

A deliberate reliability defect was introduced in `SpringDataSalaryService.java` to demonstrate detection capability:

```java
public void demoBug() {
    int a = 10 / 0;
}
```

<img width="685" height="212" alt="Screenshot 2026-03-01 211556" src="https://github.com/user-attachments/assets/636a6d7e-9afa-4df8-a2d3-783f92d4dce3" />

This creates a division-by-zero scenario which will always throw an `ArithmeticException` at runtime.

---

## Step 6 – Running Static Analysis

```bash
./mvnw clean verify -Dtest=\!SalaryApplicationTests sonar:sonar \
  -Dsonar.projectKey=salary-api \
  -Dsonar.host.url=http://localhost:9000 \
  -Dsonar.login=$SONAR_TOKEN
```

<img width="1039" height="91" alt="image" src="https://github.com/user-attachments/assets/037264c2-f269-4d33-bf23-380da36dd567" />

Maven lifecycle executed:

- Compile source code
- Run unit tests
- Generate JaCoCo coverage report
- Upload analysis report to SonarQube

---

## Step 7 – Bug Detection by SonarQube

SonarQube detected the following issue:

| Field | Value |
|---|---|
| Rule | Make sure this expression can't be zero before doing this division |
| Type | Bug |
| Category | Reliability |
| Severity | Critical |
| File | SpringDataSalaryService.java |
| Line | 37 |

<img width="1144" height="522" alt="image" src="https://github.com/user-attachments/assets/4add053c-59c2-4afe-bf24-39d5a8ef13f0" />

---

## Step 8 – Root Cause Analysis

The code performed:

```java
int a = 10 / 0;
```

<img width="1509" height="536" alt="image" src="https://github.com/user-attachments/assets/92ed3032-e612-4b2f-b8ba-c7d9fd92b5d0" />

This always throws:

```
ArithmeticException: / by zero
```

<img width="1067" height="281" alt="image" src="https://github.com/user-attachments/assets/060defe1-10b6-46ec-85ad-6b78447280eb" />

**Impact:**

| Impact | Description |
|---|---|
| Application crash | Runtime exception terminates execution |
| Interrupted flow | Downstream logic never executes |
| Reduced reliability | System stability compromised |

SonarQube detected this using symbolic execution and constant evaluation — without the code ever being run.

---

## Step 9 – Bug Fix Implementation

The code was corrected by validating the denominator before performing division:

```java
public void demoBug() {
    int denominator = 0;
    if (denominator != 0) {
        int result = 10 / denominator;
        System.out.println(result);
    } else {
        System.out.println("Denominator cannot be zero");
    }
}
```

The demo method was subsequently removed completely to restore clean code.

---

## Step 10 – Re-Analysis

After the fix, the same analysis command was executed again:

```bash
./mvnw clean verify -Dtest=\!SalaryApplicationTests sonar:sonar \
  -Dsonar.projectKey=salary-api \
  -Dsonar.host.url=http://localhost:9000 \
  -Dsonar.login=$SONAR_TOKEN
```

| Metric | Result |
|---|---|
| Bugs | 0 |
| Reliability Rating | A |
| Quality Gate | Passed |

<img width="1118" height="524" alt="image" src="https://github.com/user-attachments/assets/9943c1e3-e20a-4373-a50c-130d238e16c6" />

---

## Coverage Impact

- Coverage temporarily dropped when the untested `demoBug()` method was introduced
- After the method was removed, coverage was restored to its original level
- This demonstrates that coverage reflects executed code and is directly affected by untested methods

---

## Bug Lifecycle

| Step | Action |
|---|---|
| 1 | Introduce defect |
| 2 | Detect via static analysis |
| 3 | Classify severity |
| 4 | Perform root cause analysis |
| 5 | Implement fix |
| 6 | Re-analyze |
| 7 | Validate clean state |

---

## Observations

- Static analysis detects runtime defects without executing the code
- SonarQube uses symbolic execution and constant evaluation to find issues
- Reliability classification highlights risks that would only surface at runtime
- CI/CD integration with quality gates prevents deployment of defective code

---

## Conclusion

This POC validates that SonarQube effectively detects reliability bugs before runtime. Early defect detection enhances application stability and prevents production failures. Static bug analysis is a proactive quality control mechanism essential in modern DevOps practices.

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
| SonarQube Rules — Reliability | https://rules.sonarsource.com/java/type/Bug/ |
| JaCoCo Documentation | https://www.jacoco.org/jacoco/trunk/doc/ |
| OWASP Secure Coding Practices | https://owasp.org/www-project-secure-coding-practices-quick-reference-guide/ |
