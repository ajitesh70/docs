# Proof of Concept (POC) – Static Code Analysis in Java CI

---

| Author | Created On | Version | Last Updated By | Reviewer L0 | Reviewer L1 | Reviewer L2 |
|--------|------------|---------|-----------------|-------------|-------------|-------------|
| Ajitesh Singh | 28-02-2026 | v1 | Ajitesh Singh | Priyanshu | Faisal | Mahesh |

---

## Table of Contents

- [Objective](#objective)
- [Environment Setup](#environment-setup)
  - [Prerequisites](#prerequisites)
  - [1. Start SonarQube Server](#1-start-sonarqube-server)
  - [2. Generate a SonarQube Token](#2-generate-a-sonarqube-token)
- [Project Structure](#project-structure)
- [Step 1 – Clean Build & Run Unit Tests](#step-1--clean-build--run-unit-tests)
- [Step 2 – Run Static Code Analysis](#step-2--run-static-code-analysis-sonarqube)
- [Step 3 – View Analysis Results](#step-3--view-analysis-results)
- [POC Results](#poc-results)
- [CI Workflow Summary](#ci-workflow-summary)
- [Conclusion](#conclusion)
- [Contact Information](#contact-information)
- [References](#references)

---

## Objective

Demonstrate how **Static Code Analysis** is integrated into a Java CI workflow using:

- **Maven** — Build automation and dependency management
- **JaCoCo** — Code coverage report generation
- **SonarQube** — Static analysis, bug detection, and Quality Gate enforcement

---

## Environment Setup

### Prerequisites

| Tool | Version |
|---|---|
| Java (JDK) | 17+ |
| Maven | 3.8+ |
| SonarQube | 9.x / 10.x (Community Edition) |
| Spring Boot | 3.x |

---

### 1. Start SonarQube Server

Start SonarQube manually from the installation directory:

```bash
cd /path/to/sonarqube/bin/linux-x86-64
./sonar.sh start
```
<img width="867" height="133" alt="image" src="https://github.com/user-attachments/assets/117134b9-512d-47c0-b51e-27ee71852c42" />

Verify SonarQube is running by navigating to:

```
http://localhost:9000
```

> Default credentials: **admin / admin** (change on first login)

---

### 2. Generate a SonarQube Token

1. Log in to SonarQube at `http://localhost:9000`
2. Go to **My Account → Security → Generate Token**
3. Copy and export the token:

```bash
export SONAR_TOKEN=your_generated_token
```
<img width="923" height="31" alt="image" src="https://github.com/user-attachments/assets/f50ae857-daeb-4a18-a692-1dc55a2f97b6" />

---

## Project Structure

Java microservice configured with:

```
salary-api/
├── src/
│   ├── main/java/        # Application source code
│   └── test/java/        # Unit tests
├── pom.xml               # Maven config with JaCoCo & Sonar plugins
└── .mvn/
    └── wrapper/          # Maven Wrapper
```

### `pom.xml` — Key Plugin Configuration

```xml
<!-- JaCoCo Plugin -->
<plugin>
  <groupId>org.jacoco</groupId>
  <artifactId>jacoco-maven-plugin</artifactId>
  <version>0.8.11</version>
  <executions>
    <execution>
      <goals><goal>prepare-agent</goal></goals>
    </execution>
    <execution>
      <id>report</id>
      <phase>verify</phase>
      <goals><goal>report</goal></goals>
    </execution>
  </executions>
</plugin>

<!-- SonarQube Plugin -->
<plugin>
  <groupId>org.sonarsource.scanner.maven</groupId>
  <artifactId>sonar-maven-plugin</artifactId>
  <version>3.10.0.2594</version>
</plugin>
```

---

## Step 1 – Clean Build & Run Unit Tests

Run the Maven build with test execution:

```bash
./mvnw clean verify -Dtest=\!SalaryApplicationTests
```
<img width="1106" height="177" alt="image" src="https://github.com/user-attachments/assets/3f5813d7-7df7-450d-9b7b-7eec66c4305a" />

This command will:

- Compile all source code
- Execute unit tests
- Generate JaCoCo coverage report
- Output report to `target/site/jacoco/index.html`

> The `-Dtest=\!SalaryApplicationTests` flag excludes the Spring context integration test to keep the CI build fast.

---

## Step 2 – Run Static Code Analysis (SonarQube)

Execute the Sonar analysis:

```bash
./mvnw sonar:sonar \
  -Dsonar.projectKey=salary-api \
  -Dsonar.host.url=http://localhost:9000 \
  -Dsonar.login=$SONAR_TOKEN
```
<img width="1096" height="263" alt="image" src="https://github.com/user-attachments/assets/ea4d1a78-7ceb-4f0b-b9b3-9a25f5ccf32f" />

This performs:

- **Static code inspection** — Scans all source files against rule sets
- **Bug detection** — Identifies likely runtime errors
- **Code smell detection** — Flags maintainability issues
- **Security vulnerability analysis** — Checks against OWASP/CWE rules
- **Coverage import** — Reads JaCoCo XML and maps to source lines
- **Quality Gate evaluation** — Pass/Fail decision based on defined thresholds

---

## Step 3 – View Analysis Results

Open the SonarQube dashboard:

```
http://localhost:9000
```
<img width="1442" height="691" alt="image" src="https://github.com/user-attachments/assets/9c714237-bda4-48cf-b510-f0b1162377e0" />

Navigate to **Projects → salary-api** to review:

| Metric | Description |
|---|---|
| **Coverage %** | Percentage of code exercised by tests |
| **Code Smells** | Maintainability issues in source code |
| **Bugs** | Likely errors that will cause incorrect behavior |
| **Vulnerabilities** | Security weaknesses detected |
| **Maintainability Rating** | A–E rating based on technical debt ratio |
| **Reliability Rating** | A–E rating based on bug severity |
| **Quality Gate Status** | Overall PASS / FAIL decision |

---

## POC Results

<img width="1273" height="629" alt="image" src="https://github.com/user-attachments/assets/c509c9b2-1356-48de-9c4f-c2068cabc24a" />

The following results were captured from the SonarQube dashboard after running the full analysis on the `salary-api` project (branch: `main`).

| Metric | Result |
|---|---|
| **Coverage** | **88.2%** (on 30 lines to cover) |
| **Duplications** | **0.0%** (on 341 lines) |
| **Security — Open Issues** | 0 (0 High, 0 Medium, 0 Low) |
| **Reliability — Open Issues** | 2 (0 High, 2 Medium, 0 Low) |
| **Maintainability — Open Issues** | 15 (0 High, 6 Medium, 9 Low) |
| **Accepted Issues** | 0 |
| **Security Hotspots** | 0 |
| **Security Rating** | **A** |
| **Reliability Rating** | **A** |
| **Maintainability Rating** | **A** |

> Note: The 2 medium reliability issues and 15 maintainability issues (code smells) are flagged for review but did not affect the overall A ratings. These are candidates for the next refactoring cycle.

---

## CI Workflow Summary

<img width="676" height="497" alt="Screenshot 2026-03-01 122615" src="https://github.com/user-attachments/assets/b155018b-e207-4c57-8589-c9a01215032f" />



---

## Conclusion

This POC successfully demonstrates how static code analysis integrates into a Java CI workflow to:

- **Improve code quality** — Automated detection of bugs and smells before merge
- **Detect issues early** — Shift-left approach reduces cost of fixing defects
- **Enforce coding standards** — Consistent rules applied across all contributors
- **Block poor-quality code** — Quality Gate acts as an automated merge guardian

Static Code Analysis is a **critical component** of any modern Java CI pipeline. The combination of JaCoCo for coverage and SonarQube for deep inspection provides comprehensive visibility into code health at every commit.

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
| JaCoCo Maven Plugin Documentation | https://www.jacoco.org/jacoco/trunk/doc/maven.html |
| SonarQube Maven Scanner | https://docs.sonarsource.com/sonarqube/latest/analyzing-source-code/scanners/sonarscanner-for-maven/ |
| Maven Official Documentation | https://maven.apache.org/guides/index.html |
| OWASP — Static Analysis Tools | https://owasp.org/www-community/Source_Code_Analysis_Tools |
| SonarQube Quality Gates | https://docs.sonarsource.com/sonarqube/latest/user-guide/quality-gates/ |
