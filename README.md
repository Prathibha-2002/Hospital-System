# MedCore — Hospital Department Management System

Full-stack Hospital Management System built with **ASP.NET Core 8 Web API** + **React (Vite)** + **Tailwind CSS**.

---

## Architecture

```
hospital-system/
├── backend/          # ASP.NET Core 8 Web API
│   ├── Controllers/  # Auth, Departments, Doctors, Staff, Patients,
│   │                 # Appointments, Billing, Dashboard, AuditLogs
│   ├── Entities/     # EF Core domain models
│   ├── Repositories/ # Repository pattern (interfaces + implementations)
│   ├── Services/     # AuthService, AppointmentService, BillingService
│   ├── DTOs/         # Request/Response DTOs
│   ├── Data/         # HospitalDbContext (with auto audit trail), DbSeeder
│   └── Migrations/   # EF Core migrations
│
└── frontend/         # React 18 + Vite + Tailwind CSS
    └── src/
        ├── pages/    # Dashboard, Departments, Doctors, Staff,
        │             # Patients, Appointments, Billing, AuditLogs, Login
        ├── components/ # Layout, Sidebar
        ├── context/  # AuthContext (JWT storage + role)
        └── utils/    # api.js (Axios + JWT interceptor), toast.js
```

---

## Prerequisites

| Tool | Version |
|------|---------|
| .NET SDK | 8.0+ |
| SQL Server | 2019+ (or SQL Server Express / LocalDB) |
| Node.js | 18+ |

---

## Backend Setup

### 1. Configure connection string

Edit `backend/appsettings.json`:
```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=localhost;Database=HospitalSystemDB;Trusted_Connection=True;TrustServerCertificate=True;"
  }
}
```
Adjust `Server=` to match your SQL Server instance (e.g. `.\SQLEXPRESS` for Express).

### 2. Apply migrations & seed data

The database is **auto-migrated and seeded on first run** — no manual steps needed.

Seed data includes:
- Departments: Cardiology, Pediatrics, Neurology, Orthopedics
- Admin user: `admin` / `admin123`
- Doctor user: `doctor1` / `doctor123`  
- Staff user: `staff1` / `staff123`
- Sample patients and appointments with invoices

### 3. Run the backend

```bash
cd backend
dotnet run
```

API runs at: `http://localhost:5167`  
Swagger UI: `http://localhost:5167/swagger`

---

## Frontend Setup

### 1. Install dependencies

```bash
cd frontend
npm install
```

### 2. Run the dev server

```bash
npm run dev
```

Frontend runs at: `http://localhost:5173`

The Vite dev server proxies all `/api` calls to `http://localhost:5167` automatically.

---

## Demo Credentials

| Role | Username | Password | Access |
|------|----------|----------|--------|
| Admin | `admin` | `admin123` | Full access — Dashboard, all modules |
| Doctor | `doctor1` | `doctor123` | Doctors, Patients, Appointments |
| Staff | `staff1` | `staff123` | Doctors, Patients, Appointments, Billing |

---

## Features

### Role-Based Auth (JWT)
- Roles: `Admin`, `Doctor`, `Staff`
- JWT tokens stored in `localStorage`
- Protected routes enforce role access on both frontend and backend

### Departments (Admin only)
- Full CRUD — create, edit, delete departments
- Doctor and staff count per department

### Doctors (Admin CRUD, others read-only)
- Create doctor with linked login account
- Department assignment
- List with specialization and contact info

### Staff (Admin only)
- Full CRUD for all non-doctor staff (nurses, receptionists, etc.)
- Linked login account per staff member

### Patients
- Register and search patients
- Full edit support (all roles)
- Admin-only delete

### Appointments
- Book appointments with double-booking prevention (30-min slot check)
- Filter by status and doctor
- One-click status update (Scheduled → Completed / Cancelled)
- Auto-generates invoice on booking ($150 default)

### Billing
- View all invoices with status
- Generate invoices with base amount + 10% tax
- Mark invoices as paid (Cash, Card, Insurance, Bank Transfer)
- Revenue summary (total collected vs pending)

### Dashboard (Admin only)
- KPI cards: today's appointments, total patients, doctors, departments
- Bar chart: appointments by department
- Donut chart: doctor utilization
- Line chart: monthly revenue (last 6 months)
- Horizontal bar: appointment status breakdown
- Payment method breakdown

### Audit Log (Admin only)
- Every CREATE / UPDATE / DELETE is automatically recorded via `DbContext.SaveChanges` override
- Captures: user, timestamp, entity, old values, new values, IP
- Filterable by action type, entity, and free-text search

---

## API Endpoints Summary

| Method | Endpoint | Auth |
|--------|----------|------|
| POST | `/api/auth/login` | Public |
| POST | `/api/auth/register` | Public |
| GET/POST/PUT/DELETE | `/api/departments` | Admin (write), All (read) |
| GET/POST/PUT/DELETE | `/api/doctors` | Admin (write), All (read) |
| GET/POST/PUT/DELETE | `/api/staff` | Admin (write), All (read) |
| GET/POST/PUT/DELETE | `/api/patients` | All (write), Admin (delete) |
| GET/POST/PUT | `/api/appointments` | All |
| GET/POST/PUT | `/api/billing` | All |
| GET | `/api/dashboard/stats` | Admin |
| GET | `/api/auditlogs` | Admin |
