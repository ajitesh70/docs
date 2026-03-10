# POC – Python CI Code Compilation (Attendance API & Notification Worker)

---

| Author | Created On | Version | Last Updated By | Reviewer L0 | Reviewer L1 | Reviewer L2 |
|--------|------------|---------|-----------------|-------------|-------------|-------------|
| Ajitesh Singh | 09-03-2026 | v1 | Ajitesh Singh | Priyanshu | Faisal | Mahesh |

---

## Table of Contents

- [Objective](#objective)
- [Pre-requisites](#pre-requisites)
- [Python CI Compilation Document](#python-ci-compilation-document)
- [Part 1 – Attendance API](#part-1--attendance-api)
  - [Step 1 – Clone Repository](#step-1--clone-repository)
  - [Step 2 – Navigate to Project Directory](#step-2--navigate-to-project-directory)
  - [Step 3 – Verify Python Files](#step-3--verify-python-files)
  - [Step 4 – Run Compilation Check](#step-4--run-compilation-check)
  - [Step 5 – Verify Bytecode Generation](#step-5--verify-bytecode-generation)
  - [Step 6 – Check Compiled Files](#step-6--check-compiled-files)
- [Part 2 – Notification Worker](#part-2--notification-worker)
  - [Step 1 – Clone Repository](#step-1--clone-repository-1)
  - [Step 2 – Navigate to Project Directory](#step-2--navigate-to-project-directory-1)
  - [Step 3 – Verify Python Files](#step-3--verify-python-files-1)
  - [Step 4 – Run Compilation Check](#step-4--run-compilation-check-1)
  - [Step 5 – Verify Bytecode Generation](#step-5--verify-bytecode-generation-1)
  - [Step 6 – Check Compiled Files](#step-6--check-compiled-files-1)
- [Result](#result)
- [Conclusion](#conclusion)
- [Contact Information](#contact-information)
- [References](#references)

---

## Objective

To validate Python source code compilation for two microservices — `attendance-api` and `notification-worker` — using the built-in `compileall` module as an early CI quality gate that confirms all `.py` files are syntactically correct before proceeding to testing or deployment.

---

## Pre-requisites

| Requirement | Details |
|---|---|
| Python 3.11 | Installed on the machine |
| Git | Installed and configured |
| Internet Access | Required to clone repositories |
| Ubuntu / Linux Machine | Execution environment |

---

## Python CI Compilation Document

For a detailed understanding of Python code compilation in CI, refer to the **[Python CI Compilation Documentation](./python-ci-compilation-documentation.md)** which includes Introduction, Tools, Comparison, Best Practices, and Recommendations.

---

## Part 1 – Attendance API

**Repository:** https://github.com/OT-MICROSERVICES/attendance-api

### Step 1 – Clone Repository

```bash
git clone https://github.com/OT-MICROSERVICES/attendance-api.git
```

<img width="1102" height="180" alt="Screenshot 2026-03-09 194830" src="https://github.com/user-attachments/assets/48f8bbc4-cb5d-4853-a67b-f3538daf6b42" />


---

### Step 2 – Navigate to Project Directory

```bash
cd attendance-api
```
<img width="530" height="29" alt="Screenshot 2026-03-09 194837" src="https://github.com/user-attachments/assets/3785ca04-1f98-400b-8968-4e2b33885bdb" />

---

### Step 3 – Verify Python Files

Count all Python files in the repository:

```bash
find . -name "*.py" | wc -l
```

Expected output:

```
21
```

<img width="777" height="82" alt="Screenshot 2026-03-09 194928" src="https://github.com/user-attachments/assets/dce478d2-fc90-4d45-b63b-cb208d66904d" />


---

### Step 4 – Run Compilation Check

```bash
python3 -m compileall .
```

Expected output:

```
Compiling './app.py'
Compiling './client/postgres/postgres_conn.py'
Compiling './client/redis/redis_conn.py'
Compiling './models/message.py'
Compiling './models/user_info.py'
Compiling './router/attendance.py'
Compiling './router/cache.py'
Compiling './utils/json_encoder.py'
Compiling './utils/validator.py'
```

<img width="616" height="764" alt="Screenshot 2026-03-09 194858" src="https://github.com/user-attachments/assets/b076b11c-e336-4e69-9d62-9ca93583aee7" />


---

### Step 5 – Verify Bytecode Generation

```bash
find . -name "__pycache__"
```

Expected output:

```
./router/__pycache__
./models/__pycache__
./client/postgres/__pycache__
./utils/__pycache__
```

<img width="757" height="285" alt="Screenshot 2026-03-09 195028" src="https://github.com/user-attachments/assets/96498fa2-94a7-43f4-947c-bd6a0b9afb40" />


---

### Step 6 – Check Compiled Files

```bash
ls router/__pycache__
```

Expected output:

```
attendance.cpython-311.pyc
cache.cpython-311.pyc
```

<img width="694" height="68" alt="Screenshot 2026-03-09 195043" src="https://github.com/user-attachments/assets/b4931b9b-c650-4779-9b82-94081b3f7b90" />


---

## Part 2 – Notification Worker

**Repository:** https://github.com/OT-MICROSERVICES/notification-worker

### Step 1 – Clone Repository

```bash
git clone https://github.com/OT-MICROSERVICES/notification-worker.git
```

<img width="1253" height="160" alt="Screenshot 2026-03-09 201032" src="https://github.com/user-attachments/assets/0a7e79e5-e6b9-40a1-a988-17b43f4af19a" />


---

### Step 2 – Navigate to Project Directory

```bash
cd notification-worker
```
<img width="879" height="33" alt="Screenshot 2026-03-09 201059" src="https://github.com/user-attachments/assets/d696e68c-367b-4d9e-96bd-6cb90e880014" />

---

### Step 3 – Verify Python Files

```bash
find . -name "*.py" | wc -l
```

Expected output:

```
1
```

<img width="1018" height="48" alt="Screenshot 2026-03-09 201107" src="https://github.com/user-attachments/assets/802f83ff-e2a6-4319-ac70-7a450931a418" />


---

### Step 4 – Run Compilation Check

```bash
python3 -m compileall .
```

Expected output:

```
Compiling './notification_api.py'
```

<img width="974" height="466" alt="Screenshot 2026-03-09 201122" src="https://github.com/user-attachments/assets/dec86d9f-fe94-4863-ab2f-6dcb562aa160" />


---

### Step 5 – Verify Bytecode Generation

```bash
find . -name "__pycache__"
```

Expected output:

```
./__pycache__
```
<img width="999" height="53" alt="Screenshot 2026-03-09 201132" src="https://github.com/user-attachments/assets/eb907b2b-ccd8-4eb4-b3db-3fc16a99a6d8" />


---

### Step 6 – Check Compiled Files

```bash
ls __pycache__
```

Expected output:

```
notification_api.cpython-311.pyc
```

<img width="834" height="55" alt="Screenshot 2026-03-09 201140" src="https://github.com/user-attachments/assets/9dae0b0f-5ba8-4d96-9821-b1b2df03bbff" />


---

## Result

| Repository | Python Files | Compilation Status | Bytecode Generated |
|---|---|---|---|
| attendance-api | 21 | Passed | Yes — multiple `__pycache__` directories |
| notification-worker | 1 | Passed | Yes — `./__pycache__` |

If any syntax error exists, compilation fails with:

```
SyntaxError: invalid syntax
  File "./path/to/file.py", line X
```

---

## Conclusion

Both `attendance-api` and `notification-worker` repositories passed Python code compilation with zero errors. All 22 Python files across both repositories were successfully compiled into `.pyc` bytecode. This confirms the codebase is syntactically correct and ready for the next CI stages — unit testing and deployment.

---

## Contact Information

| Name | Email |
|------|-------|
| Ajitesh Singh | ajitesh.singh.snaatak@mygurukulam.co |

---

## References

| Title | Link |
|---|---|
| Python compileall Documentation | https://docs.python.org/3/library/compileall.html |
| Attendance API Repository | https://github.com/OT-MICROSERVICES/attendance-api |
| Notification Worker Repository | https://github.com/OT-MICROSERVICES/notification-worker |
