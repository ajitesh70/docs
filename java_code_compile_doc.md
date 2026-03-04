# Code Compilation – Detailed Documentation

---

| Author | Created On | Version | Last Updated By | Reviewer L0 | Reviewer L1 | Reviewer L2 |
|--------|------------|---------|-----------------|-------------|-------------|-------------|
| Ajitesh Singh | 05-01-2026 | v1 | Ajitesh Singh | Priyanshu | Faisal | Mahesh |

---

## Table of Contents

- [Introduction](#introduction)
- [What is Code Compilation?](#what-is-code-compilation)
- [Why Code Compilation?](#why-code-compilation)
- [Workflow](#workflow)
- [Different Tools](#different-tools)
- [Comparison of Tools](#comparison-of-tools)
- [Advantages](#advantages)
- [Proof of Concept (POC)](#proof-of-concept-poc)
- [Best Practices](#best-practices)
- [Recommendation & Conclusion](#recommendation--conclusion)
- [Contact Information](#contact-information)
- [References](#references)

---

## Introduction

Code compilation is a fundamental step in the software development lifecycle. It transforms human-readable source code into machine-executable bytecode or binaries. In Java-based projects, this process is managed by build tools like Maven or Gradle, which also handle dependency resolution, testing, and artifact packaging. Automating compilation as part of a CI pipeline ensures consistent, reproducible builds across all environments.

---

## What is Code Compilation?

Code compilation is the process of converting Java source code (`.java` files) into bytecode (`.class` files) that the Java Virtual Machine (JVM) can execute. In a Maven project, compilation is one phase in the build lifecycle.

The full build lifecycle in Maven:

```
validate → compile → test → package → verify → install → deploy
```

The `compile` phase reads source files from `src/main/java`, resolves dependencies declared in `pom.xml`, and produces `.class` files in the `target/classes` directory. The `package` phase then bundles these into a deployable JAR or WAR artifact.

---

## Why Code Compilation?

- Converts source code into executable bytecode ready for deployment
- Catches syntax and type errors before the code reaches runtime
- Resolves and downloads all project dependencies automatically
- Produces a versioned, reproducible build artifact (JAR/WAR)
- Enables consistent builds across developer machines, CI servers, and production environments
- Required as a prerequisite for testing, static analysis, and deployment stages

---

## Workflow

<img width="426" height="627" alt="image" src="https://github.com/user-attachments/assets/37d1ad97-6711-455f-a5a9-d46ce1e994df" />


---

## Different Tools

### Maven

Apache Maven is a project management and build automation tool for Java. It uses a `pom.xml` file to declare dependencies, plugins, and build lifecycle phases. The Maven Wrapper (`mvnw`) allows projects to specify and bundle a Maven version, eliminating the need for a global Maven installation.

**Best for:** Java projects requiring standardised dependency management and lifecycle management.

### Gradle

A flexible build automation tool that supports Java, Kotlin, Groovy, and Android. Uses a Groovy or Kotlin DSL instead of XML. Supports incremental builds and build caching, making it significantly faster than Maven for large projects.

**Best for:** Large multi-module projects, Android development, or teams preferring a programmatic build DSL.

### Ant

One of the oldest Java build tools. Uses XML-based `build.xml` files with explicit task definitions. Does not include built-in dependency management — typically combined with Ivy for dependency resolution. Highly flexible but verbose and manual.

**Best for:** Legacy Java projects that predate Maven and Gradle adoption.

---

## Comparison of Tools

| Feature | Maven | Gradle | Ant |
|---|---|---|---|
| Configuration | XML (`pom.xml`) | Groovy/Kotlin DSL | XML (`build.xml`) |
| Dependency Management | Built-in | Built-in | Via Ivy (external) |
| Build Lifecycle | Standardised | Flexible | Manual |
| Incremental Builds | Limited | Yes | No |
| Build Speed | Moderate | Fast | Moderate |
| Wrapper Support | Yes (`mvnw`) | Yes (`gradlew`) | No |
| Learning Curve | Low | Medium | Low |
| Community | Large | Large | Declining |

---

## Advantages

- **No global installation required** — Maven Wrapper bundles the correct Maven version with the project
- **Consistent builds** — Every developer and CI server uses the same Maven version and configuration
- **Dependency resolution** — Maven automatically downloads and caches all required libraries
- **Lifecycle management** — A single command handles validation, compilation, testing, and packaging
- **Artifact versioning** — Build outputs are versioned JAR/WAR files ready for deployment
- **CI integration** — Maven integrates natively with Jenkins, GitHub Actions, and other CI tools

---

## Proof of Concept (POC)

For the detailed POC, refer to: **[POC – Java Code Compilation Using Maven Wrapper](./POC-java-code-compilation-maven.md)**

---

## Best Practices

- **Use Maven Wrapper** — always commit `mvnw` and `.mvn/wrapper/` to the repository so all environments use the same Maven version
- **Pin dependency versions** — avoid `LATEST` or `RELEASE` version tags; use explicit versions for reproducibility
- **Use `clean` before every build** — prevents stale compiled classes from causing inconsistent behavior
- **Skip tests only when justified** — `-DskipTests` is acceptable in compilation-only POCs but should not be standard practice in CI
- **Fail fast** — configure the build to exit immediately on the first failure rather than continuing with broken output
- **Store artifacts** — publish the generated JAR to an artifact repository (Nexus, Artifactory) rather than relying on local `target/` directories
- **Separate compilation from deployment** — compile and package in CI; deploy the artifact from a separate CD stage

---

## Recommendation & Conclusion

### Recommendation

For Java projects, the recommended build setup is:

- **Maven** with **Maven Wrapper** (`mvnw`) for standardised, portable builds
- **`pom.xml`** for dependency management and plugin configuration
- Build executed in CI on every pull request using `./mvnw clean package`
- Artifact published to **Nexus** or **Artifactory** on successful builds
- **Gradle** as an alternative for large multi-module projects where build speed is a priority

### Conclusion

Code compilation is the foundation of every Java CI pipeline. Using Maven with the Maven Wrapper ensures that source code is consistently compiled, dependencies are resolved, and a deployable artifact is produced — without requiring any manual installation on the build environment. Automating this step removes human error, enforces consistency, and makes every build traceable and reproducible.

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
| Maven Build Lifecycle | https://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html |
| Gradle Documentation | https://docs.gradle.org/current/userguide/userguide.html |
| Apache Ant Documentation | https://ant.apache.org/manual/ |
