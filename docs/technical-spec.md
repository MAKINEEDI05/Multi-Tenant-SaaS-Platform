# Technical Design & Implementation Specification

**Application Name:** Multi-Tenant SaaS Project Management Platform
**Document Date:** October 26, 2025
**Release Version:** 1.0
**Prepared By:** Cloud Engineering Student / Lead Implementer

---

## 1. Repository Organization

The application is implemented as a **monolithic repository (monorepo)** that hosts both the **backend service** and the **frontend client**.
All services are coordinated using **Docker Compose** from the root directory to ensure consistent execution across environments.

---

### 1.1 Top-Level Directory Layout

```text
/Multi-Tenant-SaaS-Platform
├── docker-compose.yml       # Service orchestration (DB, API, UI)
├── submission.json          # Credentials for automated grading
├── README.md                # Project overview and usage guide
├── .gitignore               # Version control exclusions
├── docs/                    # Design, PRD, and research documents
├── backend/                 # Backend API service
└── frontend/                # Frontend React application
```

---

## 1.2 Backend Service Layout (`/backend`)

The backend is developed using **Node.js**, **Express.js**, and **Prisma ORM**, following a clean, layered structure that supports scalability and long-term maintenance.

```text
backend/
├── .env.example             # Sample environment configuration
├── Dockerfile               # Backend container definition
├── package.json             # Backend dependencies and scripts
├── prisma/
│   ├── schema.prisma        # Data model and relations
│   └── migrations/          # Versioned database migrations
├── seeds/
│   └── seed.js              # Initial data population script
└── src/
    ├── controllers/         # Request handlers and business rules
    ├── middleware/          # Auth, validation, and error handling
    ├── routes/              # REST API route mappings
    └── utils/               # Utility modules (JWT, hashing, helpers)
```

---

## 1.3 Frontend Application Layout (`/frontend`)

The frontend is implemented using **React** and built with **Vite** to enable fast development cycles and optimized production builds.

```text
frontend/
├── Dockerfile               # Frontend container definition
├── package.json             # UI dependencies
├── public/                  # Static assets (HTML, icons)
└── src/
    ├── context/             # Global state providers
    ├── pages/               # Application views (Login, Dashboard)
    ├── App.js               # Root component and routing logic
    └── index.js             # Application entry point
```

---

## 2. Development & Runtime Setup

---

## 2.1 Required Tools

Before running the application, ensure the following software is installed:

* **Docker Desktop** (version 4.0 or higher)
  *Mandatory for containerized execution*

* **Node.js** (version 18 LTS)
  *Required for local development tooling*

* **Git** (version 2.x or later)

---

## 2.2 Environment Configuration

Create a `.env` file inside the **`backend/`** directory.
Alternatively, default values from `docker-compose.yml` may be used.

### Mandatory Environment Variables

```ini
# Application Settings
PORT=5000
NODE_ENV=development

# Database (Docker Internal Network)
DATABASE_URL="postgresql://postgres:postgres@database:5432/saas_db?schema=public"

# Authentication
JWT_SECRET="replace_with_a_strong_secret_key_min_32_chars"
JWT_EXPIRES_IN="24h"

# Client Origin
FRONTEND_URL="http://localhost:3000"
```

---

## 2.3 Source Code Setup

### Clone the Repository

```bash
git clone <repository_url>
cd saas-platform
```

---

### Optional: Install Dependencies Locally

This step is optional and recommended only if you plan to modify the code with IDE support.

#### Backend

```bash
cd backend
npm install
```

#### Frontend

```bash
cd ../frontend
npm install
```

---

## 2.4 Running the Application (Docker – Recommended)

The complete stack runs using **Docker Compose**, which wires together the database, API server, and UI.

### Build and Launch Services

From the project root:

```bash
docker-compose up -d --build
```

---

### Validate Running Containers

Confirm that all services are active:

```bash
docker-compose ps
```

---

### Startup Automation

When the backend container starts, it automatically performs:

* Database schema migration via Prisma
* Initial data seeding

⏳ Allow **30–60 seconds** for the database to be fully initialized.

---

## 3. Application Endpoints

* **Web Interface:** [http://localhost:3000](http://localhost:3000)
* **Backend API:** [http://localhost:5000](http://localhost:5000)
* **System Health Check:** [http://localhost:5000/api/health](http://localhost:5000/api/health)

---

## 4. Testing & Verification

Since the application runs inside containers, testing is performed against **live services**.

---

### API Validation (Manual)

* Use credentials from `submission.json` to verify authentication
* Validate API availability using the health endpoint

#### Sample Health Check

```bash
curl http://localhost:5000/api/health
```

**Expected Response:**

```json
{
  "status": "ok",
  "database": "connected"
}
```

---

### Database Validation

To inspect the database directly, connect to the PostgreSQL container:

```bash
docker exec -it database psql -U postgres -d saas_db
```

Example query:

```sql
SELECT * FROM tenants;
```

---
