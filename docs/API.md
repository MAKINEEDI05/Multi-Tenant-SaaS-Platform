# Multi-Tenant SaaS Platform – REST API Guide

## Security & Authentication

* **Authorization Type:** JWT (Bearer Token)
* **Request Header Format:**
  `Authorization: Bearer <JWT_TOKEN>`
* **Token Validity:** 24 hours
* **API Base Path (Local):**
  `http://localhost:5000/api`

---

## 1. System APIs

### 1.1 Service Health Status

Verifies whether the backend service and database connection are operational.

* **Method:** `GET`
* **Route:** `/health`
* **Authorization:** Not Required

#### Successful Response (200)

```json
{
  "status": "ok",
  "database": "connected"
}
```

---

## 2. Authentication Services

### 2.1 Tenant Registration

Creates a new organization and initializes its first administrator account.

* **Method:** `POST`
* **Route:** `/auth/register-tenant`
* **Authorization:** Public

#### Request Payload

```json
{
  "tenantName": "Acme Corp",
  "subdomain": "acme",
  "adminEmail": "admin@acme.com",
  "password": "SecurePassword123"
}
```

#### Response (201)

```json
{
  "message": "Tenant registration completed",
  "tenantId": "uuid-string"
}
```

---

### 2.2 User Login

Validates user credentials and issues a JWT token.

* **Method:** `POST`
* **Route:** `/auth/login`
* **Authorization:** Public

#### Request Payload

```json
{
  "email": "admin@acme.com",
  "password": "SecurePassword123"
}
```

#### Response (200)

```json
{
  "token": "jwt-token-string",
  "user": {
    "id": "uuid",
    "email": "admin@acme.com",
    "role": "tenant_admin",
    "tenantId": "uuid"
  }
}
```

---

### 2.3 Fetch Logged-In User

Returns profile details of the authenticated user.

* **Method:** `GET`
* **Route:** `/auth/me`
* **Authorization:** Required (All Roles)

#### Response (200)

```json
{
  "user": {
    "id": "uuid",
    "fullName": "John Doe",
    "email": "john@acme.com",
    "role": "user"
  }
}
```

---

## 3. Tenant Administration (System Admin Only)

### 3.1 Retrieve All Tenants

Provides a list of every registered tenant in the system.

* **Method:** `GET`
* **Route:** `/tenants`
* **Authorization:** Super Admin

#### Response (200)

```json
{
  "status": "success",
  "count": 2,
  "tenants": [
    { "id": "1", "name": "Acme Corp", "subdomain": "acme" },
    { "id": "2", "name": "Beta Inc", "subdomain": "beta" }
  ]
}
```

---

### 3.2 Tenant Information

Fetches detailed data for a specific tenant.

* **Method:** `GET`
* **Route:** `/tenants/:id`
* **Authorization:** Super Admin

#### Response (200)

```json
{
  "id": "uuid",
  "name": "Acme Corp",
  "status": "active"
}
```

---

### 3.3 Modify Tenant

Updates tenant metadata such as name or operational status.

* **Method:** `PUT`
* **Route:** `/tenants/:id`
* **Authorization:** Super Admin

#### Request Payload

```json
{
  "name": "Acme Global",
  "status": "inactive"
}
```

---

## 4. Tenant User Administration (Tenant Admin)

### 4.1 View Tenant Users

Lists all users belonging to the current tenant.

* **Method:** `GET`
* **Route:** `/tenants/:tenantId/users`
* **Authorization:** Tenant Admin

#### Response (200)

```json
{
  "users": [
    { "id": "u1", "fullName": "Alice", "role": "user" }
  ]
}
```

---

### 4.2 Add New User

Creates a new user under the tenant.

* **Method:** `POST`
* **Route:** `/tenants/:tenantId/users`
* **Authorization:** Tenant Admin

#### Request Payload

```json
{
  "email": "alice@acme.com",
  "password": "Password123",
  "fullName": "Alice Smith",
  "role": "user"
}
```

---

### 4.3 Update User Profile

Edits user details or role assignment.

* **Method:** `PUT`
* **Route:** `/users/:id`
* **Authorization:** Tenant Admin

#### Request Payload

```json
{
  "fullName": "Alice Jones",
  "role": "tenant_admin"
}
```

---

### 4.4 Remove User

Deletes a user from the tenant workspace.

* **Method:** `DELETE`
* **Route:** `/users/:id`
* **Authorization:** Tenant Admin

---

## 5. Project APIs

### 5.1 Retrieve Projects

Fetches all projects associated with the tenant.

* **Method:** `GET`
* **Route:** `/projects`
* **Authorization:** User / Admin

#### Response (200)

```json
{
  "projects": [
    { "id": "p1", "title": "Website Redesign", "status": "active" }
  ]
}
```

---

### 5.2 Create Project

Creates a new project under the tenant.

* **Method:** `POST`
* **Route:** `/projects`
* **Authorization:** Admin

#### Request Payload

```json
{
  "title": "Q3 Marketing Campaign",
  "description": "Planning for Q3",
  "status": "active"
}
```

---

### 5.3 Project Details

Returns complete information for a project.

* **Method:** `GET`
* **Route:** `/projects/:id`
* **Authorization:** User / Admin

---

### 5.4 Update Project

Updates project attributes such as status.

* **Method:** `PUT`
* **Route:** `/projects/:id`
* **Authorization:** Admin

#### Request Payload

```json
{
  "status": "completed"
}
```

> **Note:**
> Project deletion is typically implemented using
> `DELETE /projects/:id`

---

## 6. Task APIs

### 6.1 Retrieve Tasks

Lists all tasks under a specific project.

* **Method:** `GET`
* **Route:** `/projects/:projectId/tasks`
* **Authorization:** User / Admin

#### Response (200)

```json
{
  "tasks": [
    { "id": "t1", "title": "Draft content", "status": "TODO" }
  ]
}
```

---

### 6.2 Create Task

Adds a task to a project.

* **Method:** `POST`
* **Route:** `/projects/:projectId/tasks`
* **Authorization:** Admin

#### Request Payload

```json
{
  "title": "Fix Header Bug",
  "description": "CSS issue on mobile",
  "priority": "HIGH",
  "dueDate": "2023-12-31"
}
```

---

### 6.3 Change Task Status

Updates only the task state (useful for Kanban workflows).

* **Method:** `PATCH`
* **Route:** `/tasks/:id/status`
* **Authorization:** User / Admin

#### Request Payload

```json
{
  "status": "IN_PROGRESS"
}
```

---

### 6.4 Update Task Details

Performs a complete update on a task.

* **Method:** `PUT`
* **Route:** `/tasks/:id`
* **Authorization:** Admin

#### Request Payload

```json
{
  "title": "Fix Header Bug (Updated)",
  "priority": "MEDIUM"
}
```
