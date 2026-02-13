# Frontend Deployment POC  
## React Dashboard Integrated with Microservices

---

## 📌 Objective

This Proof of Concept (POC) demonstrates the complete deployment of a React-based frontend application integrated with:

- Employee API (Go)
- Attendance API (Python)
- Salary API (Spring Boot)

The frontend is deployed on:

- Ubuntu 22.04 (AWS EC2)
- Node.js 16 (Build Environment)
- Nginx (Reverse Proxy + Static Hosting)

---

## 🏗 Architecture Overview

```
Browser
   ↓
Nginx (Port 80)
   ↓
React Production Build (Static Files)
   ↓
Reverse Proxy Routing
   ├── /employee   → Employee API (Go)
   ├── /attendance → Attendance API (Python)
   └── /salary     → Salary API (Spring Boot)
```

---

# 🚀 Deployment Steps

---

## Step 1 – Launch EC2 Instance

### Instance Details
- OS: Ubuntu 22.04
- Instance Type: t2.micro or above
- Public IP: Enabled

### Security Group Configuration

Allow inbound:

| Port | Purpose |
|------|---------|
| 22   | SSH     |
| 80   | HTTP    |

---

## Step 2 – Connect to Instance

```bash
ssh -i projectkey.pem ubuntu@<PUBLIC-IP>
```

---

## Step 3 – Install Node.js 16

Project uses older React + Webpack dependencies.  
Node 16 ensures stable builds.

### Install Node 16

```bash
curl -fsSL https://deb.nodesource.com/setup_16.x | sudo -E bash -
sudo apt install nodejs -y
```

### Verify Installation

```bash
node -v
npm -v
```

Expected:
```
v16.x.x
```

---

## Step 4 – Clone Frontend Repository

```bash
git clone https://github.com/OT-MICROSERVICES/frontend.git
cd frontend
```

Install dependencies:

```bash
npm install
```

---

## Step 5 – Build Production Version

```bash
npm run build
```

Build output directory:

```
/home/ubuntu/frontend/build
```

---

## Step 6 – Install Nginx

```bash
sudo apt update
sudo apt install nginx -y
```

---

## Step 7 – Configure Nginx (Reverse Proxy + Static Hosting)

Create config file:

```bash
sudo nano /etc/nginx/sites-available/frontend
```

Paste:

```nginx
server {
    listen 80;
    server_name _;

    root /home/ubuntu/frontend/build;
    index index.html;

    # Employee API
    location /employee/ {
        proxy_pass http://10.0.129.233:8080;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }

    # Attendance API
    location /attendance/ {
        proxy_pass http://10.0.128.243:8080;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }

    # Salary API
    location /salary/ {
        proxy_pass http://10.0.11.43:8080;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }

    # React SPA
    location / {
        try_files $uri /index.html;
    }
}
```

---

## Step 8 – Enable Configuration

```bash
sudo ln -s /etc/nginx/sites-available/frontend /etc/nginx/sites-enabled/
sudo rm /etc/nginx/sites-enabled/default
```

Test configuration:

```bash
sudo nginx -t
```

Reload Nginx:

```bash
sudo systemctl reload nginx
```

---

## Step 9 – Fix Permission Issues

If browser shows:

```
500 Internal Server Error
Permission denied
```

Fix permissions:

```bash
sudo chown -R www-data:www-data /home/ubuntu/frontend/build
sudo chmod -R 755 /home/ubuntu/frontend
sudo systemctl reload nginx
```

---

## Step 10 – API Proxy Testing

Test from server:

```bash
curl http://localhost/salary/api/v1/salary/search/all
```

If JSON appears → proxy configuration is correct.

---

## Step 11 – Access in Browser

```
http://<PUBLIC-IP>
```
<img width="1920" height="1080" alt="Screenshot (325)" src="https://github.com/user-attachments/assets/00a1ed79-a994-4994-86a6-151ecaed88f6" />


Test routes:

```
/employee-list
/attendance-list
/salary-list
```
<img width="1920" height="1080" alt="Screenshot (311)" src="https://github.com/user-attachments/assets/fb686740-8e79-436a-bf81-d19027cbed2c" />
<img width="1920" height="1080" alt="Screenshot (312)" src="https://github.com/user-attachments/assets/9df5d880-2df4-432a-9edb-97ccfc41cd09" />
<img width="1920" height="1080" alt="Screenshot (313)" src="https://github.com/user-attachments/assets/401a378c-c566-41ea-9174-dcea0e18d762" />

---

# 🔧 Source Code Changes (Important)

## 1️⃣ ListSalary.js Changes

### Old (Incorrect)
```javascript
fetch('/salary/search/all')
```

### Updated (Correct)
```javascript
fetch("/salary/api/v1/salary/search/all")
  .then((response) => {
    if (!response.ok) {
      throw new Error(`HTTP error ${response.status}`);
    }
    return response.json();
  })
  .then((data) => {
    this.setState({ data });
  })
  .catch((err) => {
    console.error("Salary fetch failed:", err);
  });
```

### Field Mapping Fix

Old:
```javascript
<Table.Col>{item.annual_package}</Table.Col>
```

New:
```javascript
<Table.Col>{item.salary}</Table.Col>
```

---

## 2️⃣ EmployeeList.js Route Fix

```javascript
fetch("/employee/api/v1/employee/search/all")
```

---

## 3️⃣ AttendanceList.js Route Fix

```javascript
fetch("/attendance/api/v1/attendance/search/all")
```

---

## 4️⃣ EmployeeData.js Improvements

- Uses shared `useEmployees()` hook
- Calculates:
  - Total Employees
  - Active Employees
  - Ex Employees
  - Role Distribution
  - Location Distribution

Charts implemented using:

```javascript
import C3Chart from "react-c3js";
```

Dynamic mapping replaces hardcoded values.

---

## 5️⃣ App.react.js Routing Validation

```javascript
<ApmRoute exact path="/salary-list" component={ListSalary} />
```

Ensures correct navigation.

---

# 🔄 Differences Between Old and New Frontend

| Component | Old | New |
|------------|------|------|
| Salary API Path | /salary/search/all | /salary/api/v1/salary/search/all |
| Salary Field | annual_package | salary |
| Employee Routes | No /api/v1 | Added /api/v1 |
| Error Handling | Minimal | HTTP status validation |
| Charts | Hardcoded | Dynamic mapping |

---

# 🛠 Issues Faced & Resolution

## 404 Not Found
Cause:
- Wrong proxy_pass URL
- Wrong API route in React

Fix:
- Updated Nginx location blocks
- Updated fetch paths

---

## Salary Not Showing
Cause:
- Incorrect field mapping
- Wrong API route

Fix:
- Changed annual_package → salary
- Updated API endpoint

---

## 500 Internal Server Error
Cause:
- Nginx permission issue

Fix:
```bash
chown www-data:www-data build
```

---

## Nginx Conflict Warning
Cause:
- Default site enabled

Fix:
```bash
sudo rm /etc/nginx/sites-enabled/default
```

---

# ✅ Final Result

- React production build served via Nginx
- Reverse proxy routing working
- Employee API integrated
- Attendance API integrated
- Salary API integrated
- Dashboard charts working
- Salary table rendering correctly
- Clean production environment

---

# 📦 Final Deployment Summary

- Node 16 used for compatibility
- React app built in production mode
- Nginx configured as reverse proxy
- Multi-microservice routing enabled
- All src changes aligned with backend APIs
- Permission and routing issues resolved

---

# 🎯 Conclusion

This POC demonstrates:

- End-to-end frontend deployment
- Multi-service reverse proxy architecture
- Production build hosting
- API path correction and integration
- Source-level corrections for full functionality
- Real-world debugging and resolution workflow

---

**Frontend Deployment POC – Successfully Completed**
