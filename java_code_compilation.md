# POC – Java Code Compilation Using Maven Wrapper (Salary API)

---

| Author | Created On | Version | Last Updated By | Reviewer L0 | Reviewer L1 | Reviewer L2 |
|--------|------------|---------|-----------------|-------------|-------------|-------------|
| Ajitesh Singh | 05-01-2026 | v1 | Ajitesh Singh | Priyanshu | Faisal | Mahesh |

---

## Table of Contents

- [Objective](#objective)
- [Environment Details](#environment-details)
- [Project Structure](#project-structure)
- [Execution Steps](#execution-steps)
- [Build Process](#build-process)
- [Build Artifact](#build-artifact)
- [Verification](#verification)
- [Outcome](#outcome)
- [Conclusion](#conclusion)
- [Contact Information](#contact-information)
- [References](#references)

---

## Objective

Demonstrate how Java code compilation and packaging can be performed using the Maven Wrapper included in the project, without installing Maven separately on the system. The compilation process ensures that Java source code is successfully converted into bytecode and packaged into a deployable JAR artifact.

---

## Environment Details

| Component | Details |
|---|---|
| Application | Salary API (Spring Boot) |
| OS | Ubuntu (EC2 Instance) |
| Java Version | OpenJDK 17 |
| Build Tool | Apache Maven |
| Build Method | Maven Wrapper (`mvnw`) |

> The project already includes Maven Wrapper (`mvnw`) — Maven installation was not required on the system.

---

## Project Structure

```
salary-api/
├── mvnw
├── mvnw.cmd
├── pom.xml
├── src/
└── target/
```

| File / Directory | Purpose |
|---|---|
| `mvnw` | Maven Wrapper script |
| `pom.xml` | Maven project configuration |
| `src/` | Java source code |
| `target/` | Build output directory |

---

## Execution Steps

### Step 1 – Navigate to Project Directory

```bash
cd ~/salary-api
```

### Step 2 – Provide Execute Permission to Maven Wrapper

```bash
chmod +x mvnw
```
<img width="752" height="33" alt="Screenshot 2026-03-04 210028" src="https://github.com/user-attachments/assets/ddff335d-7c0d-4106-92c5-06a3e30657e6" />


### Step 3 – Run Maven Build Using Wrapper

```bash
./mvnw clean package -DskipTests
```
<img width="1071" height="241" alt="Screenshot 2026-03-04 210006" src="https://github.com/user-attachments/assets/3fc96efb-e6b1-4232-a3e5-c6ba587a6a28" />

<img width="1154" height="247" alt="image" src="https://github.com/user-attachments/assets/e070a2b0-969c-45ee-9047-82c2133aae61" />


| Parameter | Purpose |
|---|---|
| `clean` | Removes previous build artifacts |
| `package` | Compiles code and creates a JAR |
| `-DskipTests` | Skips unit test execution |

> Tests were skipped because the application requires a Cassandra database connection which was not configured for this POC.

---

## Build Process

When the command runs, Maven performs the following phases in order:

```
validate → compile → test (skipped) → package
```

Steps performed internally:

1. Read project configuration from `pom.xml`
2. Download project dependencies
3. Compile Java source code
4. Package compiled classes into a JAR file

---

## Build Artifact

After successful execution, the artifact is generated in the `target/` directory.

```bash
ls target
```
<img width="1347" height="112" alt="Screenshot 2026-03-04 210243" src="https://github.com/user-attachments/assets/c1b12f4c-9f68-4e4b-86ac-6ef3a2ffad39" />

Expected output:

```
salary-0.0.1-SNAPSHOT.jar
```

This JAR file represents the fully packaged application ready for deployment.

---

## Verification

Compilation can be verified by checking the compiled `.class` files:

```bash
ls target/classes/
```
<img width="698" height="94" alt="image" src="https://github.com/user-attachments/assets/bbf6abe2-3387-469c-b70b-3d863b13edd7" />

These files are the compiled bytecode generated from the Java source code.

---

## Outcome

| Result | Status |
|---|---|
| Java source code compiled |  Success |
| Maven Wrapper used (no global Maven install) |  Success |
| JAR artifact generated |  Success |
| Build completed without errors |  Success |

---

## Conclusion

This POC demonstrated that Java code compilation and packaging can be performed using the Maven Wrapper included within the project. Using the wrapper ensures consistent Maven versions across environments and eliminates the need for manual Maven installation. The generated JAR artifact confirms that the application was compiled and packaged successfully.

---

## Contact Information

| Name | Email |
|------|-------|
| Ajitesh Singh | ajitesh.singh.snaatak@mygurukulam.co |

---

## References

| Title | Link |
|---|---|
| Apache Maven Documentation | https://maven.apache.org/guides/ |
| Maven Wrapper Documentation | https://maven.apache.org/wrapper/ |
| Spring Boot Build Documentation | https://docs.spring.io/spring-boot/docs/current/reference/html/build-tool-plugins.html |
| OpenJDK 17 | https://openjdk.org/projects/jdk/17/ |
