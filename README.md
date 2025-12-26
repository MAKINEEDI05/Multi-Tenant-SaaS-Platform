# Multi-Tenant Enterprise SaaS System

## Overview

This project is a **B2B Software-as-a-Service platform** designed to showcase a real-world implementation of **multi-tenant architecture**, **strict data separation**, and **role-based authorization**.
Each organization (tenant) operates in its own isolated environment, managing users, projects, and tasks without any cross-tenant data exposure.

The system enforces a clear access hierarchy:

* **System Admin** oversees all tenants
* **Organization Admins** manage their company’s workspace
* **Team Members** collaborate on assigned work items

---

## Core Capabilities

### 🔐 Tenant Isolation

* Every request is scoped using a unique `tenant_id`
* Ensures complete data separation between organizations

### 🔑 Authentication & Security

* Secure login using **JWT tokens**
* Passwords stored using **bcrypt hashing**
* Token-based session handling

### 🧑‍💼 Role-Based Authorization (RBAC)

* Fine-grained permission control
* Access rules enforced at API level
* Roles: System Admin, Tenant Admin, User

### 📁 Project Lifecycle Management

* Create and manage projects per tenant
* Track progress and ownership

### 📝 Task Assignment & Tracking

* Assign tasks to users
* Support for priority levels and deadlines
* Status updates for workflow tracking

### 👥 Organization User Management

* Tenant Admins can onboard or remove employees
* Role assignment within the tenant

### 🌍 Global Admin Console

* Centralized view of all registered tenants
* System-level monitoring and oversight

### 📱 Responsive Web Interface

* Modern dashboard UI
* Optimized for desktop and tablet usage

---

## Technology Stack

### Frontend

* **Library:** React.js (v18)
* **Routing:** React Router v6
* **State Handling:** Context API
* **API Calls:** Axios
* **Styling:** Responsive CSS (Flexbox & Grid)

### Backend

* **Runtime:** Node.js v18
* **Framework:** Express.js
* **ORM:** Prisma
* **Security:** JWT, Bcrypt, CORS
* **Validation:** Express-Validator

### Database & Infrastructure

* **Database:** PostgreSQL v15
* **Containers:** Docker & Docker Compose
* **Base Images:** Lightweight Linux (Alpine)

---

## System Architecture

The platform follows a **decoupled client-server model**.

* The **React frontend** consumes RESTful APIs
* The **Node.js backend** processes requests and enforces RBAC
* The **PostgreSQL database** stores tenant-scoped data
* All queries are filtered by `tenant_id` to avoid leakage

---

## Setup & Deployment

### Requirements

* Docker & Docker Compose *(recommended)*
* Node.js v18+
* PostgreSQL (for non-Docker setup)

---

### Option 1: Docker-Based Setup (Recommended)

1. **Clone the Project**

   ```bash
   git clone <your-repo-url>
   cd multi-tenant-saas
   ```

2. **Environment Configuration**
   Create a `.env` file in the root directory:

   ```env
   POSTGRES_USER=postgres
   POSTGRES_PASSWORD=postgres
   POSTGRES_DB=saas_db
   ```

3. **Start Services**

   ```bash
   docker-compose up -d --build
   ```

4. **Application Access**

   * Frontend: [http://localhost:3000](http://localhost:3000)
   * Backend Health API: [http://localhost:5000/api/health](http://localhost:5000/api/health)

   ✅ Database migrations and seed scripts run automatically

---

### Option 2: Local Development

<details>
<summary>Manual setup steps</summary>

#### Database

Ensure PostgreSQL is running on port `5432`.

#### Backend

```bash
cd backend
npm install
cp .env.example .env
npx prisma migrate dev --name init
npm run seed
npm start
```

#### Frontend

```bash
cd frontend
npm install
npm start
```

</details>

---

## Environment Configuration

| Variable       | Purpose                      | Docker Default                                 |
| -------------- | ---------------------------- | ---------------------------------------------- |
| `PORT`         | Backend API port             | 5000                                           |
| `DATABASE_URL` | PostgreSQL connection string | Internal Docker DB                             |
| `JWT_SECRET`   | Token signing key            | Custom                                         |
| `FRONTEND_URL` | CORS origin                  | [http://localhost:3000](http://localhost:3000) |

---

## REST API Overview

### Authentication

* `POST /api/auth/register-tenant` – Create a new organization
* `POST /api/auth/login` – Authenticate user

### Projects & Tasks

* `GET /api/projects` – Fetch tenant projects
* `POST /api/projects` – Create project
* `POST /api/projects/:id/tasks` – Add task
* `PATCH /api/tasks/:id/status` – Update task status

### Administration

* `GET /api/tenants` – *(System Admin)* View all tenants
* `GET /api/tenants/:id/users` – *(Tenant Admin)* List users
* `POST /api/tenants/:id/users` – *(Tenant Admin)* Add user

---

## Demo Login Accounts

### System Administrator

* **Email:** [superadmin@system.com](mailto:superadmin@system.com)
* **Password:** Admin@123

### Demo Organization Admin

* **Email:** [admin@demo.com](mailto:admin@demo.com)
* **Password:** Admin@123
* **Tenant Code:** demo
