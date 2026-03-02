# Proof of Concept (POC) – Bug Analysis Using SonarQube

---

| Author | Created On | Version | Last Updated By | Reviewer L0 | Reviewer L1 | Reviewer L2 |
|--------|------------|---------|-----------------|-------------|-------------|-------------|
| Ajitesh Singh | 05-01-2026 | v1 | Ajitesh Singh | Priyanshu | Faisal | Mahesh |

---

## Table of Contents

- [Objective](#objective)
- [Environment Setup](#environment-setup)
- [Step 1 – Introducing a Controlled Bug](#step-1--introducing-a-controlled-bug)
- [Step 2 – Running Static Analysis](#step-2--running-static-analysis)
- [Step 3 – Bug Detection by SonarQube](#step-3--bug-detection-by-sonarqube)
- [Step 4 – Root Cause Analysis](#step-4--root-cause-analysis)
- [Step 5 – Bug Fix Implementation](#step-5--bug-fix-implementation)
- [Step 6 – Re-Analysis](#step-6--re-analysis)
- [Coverage Impact](#coverage-impact)
- [Bug Lifecycle](#bug-lifecycle)
- [Observations](#observations)
- [Conclusion](#conclusion)
- [Contact Information](#contact-information)
- [References](#references)

---

## Objective

To demonstrate:

- How static analysis detects runtime reliability defects
- How severity is classified
- How root cause analysis is performed
- How bug resolution is verified through re-analysis

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

**Initial project state (before introducing bug):**

- Bugs = 0
- Reliability Rating = A
- Quality Gate = Passed

<img width="1130" height="519" alt="image" src="https://github.com/user-attachments/assets/619a133b-421b-4b17-a70d-c35cd2a2442e" />
<img width="333" height="144" alt="image" src="https://github.com/user-attachments/assets/97f5c7a3-5e46-46a2-8185-ea2610584118" />

---

## Step 1 – Introducing a Controlled Bug

A deliberate reliability defect was introduced in `SpringDataSalaryService.java` to demonstrate detection capability:

```java
public void demoBug() {
    int a = 10 / 0;
}
```
<img width="685" height="212" alt="Screenshot 2026-03-01 211556" src="https://github.com/user-attachments/assets/636a6d7e-9afa-4df8-a2d3-783f92d4dce3" />

This creates a division-by-zero scenario which will always throw an `ArithmeticException` at runtime.

---

## Step 2 – Running Static Analysis

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

## Step 3 – Bug Detection by SonarQube

SonarQube detected the following issue:

| Field | Value |
|---|---|
| **Rule** | Make sure this expression can't be zero before doing this division |
| **Type** | Bug |
| **Category** | Reliability |
| **Severity** | Critical |
| **File** | SpringDataSalaryService.java |
| **Line** | 37 |

---
<img width="1144" height="522" alt="image" src="https://github.com/user-attachments/assets/4add053c-59c2-4afe-bf24-39d5a8ef13f0" />

## Step 4 – Root Cause Analysis

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

- Application crash at runtime
- Interrupted execution flow
- Reduced system reliability

SonarQube detected this using symbolic execution and constant evaluation — without the code ever being run.

---

## Step 5 – Bug Fix Implementation

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

## Step 6 – Re-Analysis

After the fix, the same analysis command was executed again.

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

This POC demonstrated the complete bug lifecycle:

1. Introduce defect
2. Detect via static analysis
3. Classify severity
4. Perform root cause analysis
5. Implement fix
6. Re-analyze
7. Validate clean state

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
