# team-management-system
# ◈ TaskFlow — Team Task Manager

> A full-stack, role-based task management application built with **React**, **Node.js/Express**, **PostgreSQL (Prisma)**, and deployed on **Railway**.

![Node.js](https://img.shields.io/badge/Node.js-18%2B-339933?logo=node.js&logoColor=white)
![React](https://img.shields.io/badge/React-18-61DAFB?logo=react&logoColor=black)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-Prisma-4169E1?logo=postgresql&logoColor=white)
![License](https://img.shields.io/badge/License-MIT-green)

---

## 🔗 Live Demo

| Service       | URL                                               |
|---------------|---------------------------------------------------|
| Frontend      | `https://taskflow-frontend.up.railway.app`        |
| Backend API   | `https://taskflow-backend.up.railway.app`         |
| Health Check  | `https://taskflow-backend.up.railway.app/health`  |

**Demo Credentials**

| Role   | Email            | Password   |
|--------|------------------|------------|
| Admin  | admin@demo.com   | admin123   |
| Member | member@demo.com  | member123  |
| Member | morgan@demo.com  | pass123    |

---

## 🚀 Features

### 🔐 Authentication
- JWT-based signup & login with bcrypt password hashing
- Role selection at registration: **Admin** or **Member**
- Persistent login via localStorage token
- Protected routes — unauthenticated users redirect to login

### 🛡 Role-Based Access Control

| Action                               | Admin | Member |
|--------------------------------------|:-----:|:------:|
| Create project                       | ✅    | ❌     |
| Delete own project                   | ✅    | ❌     |
| Create task                          | ✅    | ❌     |
| Edit / delete any task               | ✅    | ❌     |
| Update status of assigned task       | ✅    | ✅     |
| View projects they're part of        | ✅    | ✅     |
| View team page                       | ✅    | ✅     |

### 📁 Project Management
- Create projects with name, description, and team members
- Admins can delete their own projects (cascades to tasks)
- Progress bar per project (% tasks completed)
- Overdue task count per project

### ✅ Task Management
- Create tasks with title, description, project, assignee, status, priority, and due date
- Filter tasks by project, status, or assignee
- Overdue detection (highlighted in red)
- Priority levels: **High / Medium / Low**
- Statuses: **Todo / In Progress / Done**

### 📊 Dashboard
- Summary stats: Total tasks, In Progress, Completed, Overdue
- "My Tasks" personal panel
- "Overdue Tasks" team-wide panel
- Per-project progress bars

### 👥 Team Page
- View all workspace members with task, done, and overdue breakdown per person

---

## 🛠 Tech Stack

### Backend
| Technology     | Purpose                              |
|----------------|--------------------------------------|
| Node.js 18+    | Runtime environment                  |
| Express.js     | HTTP server & routing                |
| Prisma ORM     | Database access & migrations         |
| PostgreSQL      | Relational database                  |
| JWT            | Authentication tokens                |
| bcryptjs       | Password hashing                     |
| Helmet + CORS  | Security middleware                  |

### Frontend
| Technology     | Purpose                              |
|----------------|--------------------------------------|
| React 18       | UI framework                         |
| Vite           | Build tool & dev server              |
| Native fetch   | HTTP requests with JWT bearer tokens |
| Inline styles  | Styling (no CSS framework dependency)|

### Deployment
- **Platform**: Railway
- **Database**: Railway PostgreSQL plugin
- **Architecture**: Two separate Railway services (backend + frontend)

---

## 📁 Project Structure

```
team-task-manager/
│
├── backend/
│   ├── prisma/
│   │   ├── schema.prisma        # DB schema: User, Project, Task models
│   │   └── seed.js              # Demo data seeder
│   │
│   ├── src/
│   │   ├── middleware/
│   │   │   └── auth.js          # JWT verification middleware
│   │   │
│   │   ├── routes/
│   │   │   ├── auth.js          # POST /api/auth/register|login
│   │   │   ├── users.js         # GET  /api/users, /api/users/me
│   │   │   ├── projects.js      # CRUD /api/projects
│   │   │   └── tasks.js         # CRUD /api/tasks
│   │   │
│   │   └── index.js             # Express app entry point
│   │
│   ├── .env.example             # Required environment variables
│   ├── .gitignore
│   ├── package.json
│   └── railway.toml             # Railway deployment config
│
├── frontend/
│   ├── src/
│   │   ├── api.js               # Centralized API client (fetch wrapper)
│   │   ├── App.jsx              # Full app: Auth, Dashboard, Projects, Tasks, Team
│   │   └── main.jsx             # React DOM entry point
│   │
│   ├── index.html
│   ├── vite.config.js
│   ├── .env.example             # Required environment variables
│   ├── .gitignore
│   ├── package.json
│   └── railway.toml             # Railway deployment config
│
├── .gitignore
└── README.md
```

---

## 🗄 Database Schema

```
User
  id          String   (cuid, PK)
  name        String
  email       String   (unique)
  password    String   (bcrypt hashed)
  role        String   "Admin" | "Member"
  avatar      String
  createdAt   DateTime
  updatedAt   DateTime
  ──────────────────────────────────────
  → ownedProjects    (1:many → Project)
  → memberProjects   (many:many → Project)
  → assignedTasks    (1:many → Task)

Project
  id          String   (cuid, PK)
  name        String
  description String
  ownerId     String   (FK → User)
  createdAt   DateTime
  updatedAt   DateTime
  ──────────────────────────────────────
  → owner    (many:1 → User)
  → members  (many:many → User)
  → tasks    (1:many → Task)

Task
  id          String    (cuid, PK)
  title       String
  description String
  status      String    "Todo" | "In Progress" | "Done"
  priority    String    "High" | "Medium" | "Low"
  dueDate     DateTime? (nullable)
  projectId   String    (FK → Project, cascade delete)
  assigneeId  String?   (FK → User, nullable)
  createdAt   DateTime
  updatedAt   DateTime
```

---

## ⚙️ Local Development Setup

### Prerequisites
- **Node.js** 18 or higher
- **PostgreSQL** (local install) **or** a free [Railway](https://railway.app) project for a cloud DB

---

### 1. Clone & Install

```bash
git clone https://github.com/YOUR_USERNAME/team-task-manager.git
cd team-task-manager
```

**Backend dependencies:**
```bash
cd backend
npm install
```

**Frontend dependencies:**
```bash
cd ../frontend
npm install
```

---

### 2. Configure Environment Variables

**Backend** — copy the example and fill in your values:
```bash
cd backend
cp .env.example .env
```

Edit `backend/.env`:
```env
DATABASE_URL="postgresql://postgres:password@localhost:5432/taskmanager"
JWT_SECRET="your-super-secret-key-at-least-64-chars-long-replace-this"
PORT=3001
FRONTEND_URL="http://localhost:5173"
```

**Frontend** — for local dev, leave `VITE_API_URL` empty (Vite proxies `/api` to port 3001 automatically):
```bash
cd ../frontend
cp .env.example .env
# VITE_API_URL=   ← leave this empty for local dev
```

---

### 3. Set Up the Database

```bash
cd backend

# Push schema to your PostgreSQL database
npm run db:push

# Seed with demo users, projects, and tasks
npm run db:seed
```

---

### 4. Run Both Servers

Open **two terminal windows**:

```bash
# Terminal 1 — Backend
cd backend
npm run dev
# → Runs at http://localhost:3001
```

```bash
# Terminal 2 — Frontend
cd frontend
npm run dev
# → Runs at http://localhost:5173
```

Open `http://localhost:5173` and log in using the demo credentials above.

---

## 🚂 Railway Deployment Guide

### Step 1 — Create a Railway Project

1. Go to [railway.app](https://railway.app) → **New Project**
2. Select **Empty Project**

---

### Step 2 — Add PostgreSQL Database

1. Inside your project → click **+ New** → **Database** → **PostgreSQL**
2. Railway auto-provisions the DB and injects `DATABASE_URL` into your environment

---

### Step 3 — Deploy the Backend

1. **+ New** → **GitHub Repo** → select your repository → set root directory to `/backend`
2. Go to the **Variables** tab and add:

```
JWT_SECRET=your-very-long-random-secret-here
FRONTEND_URL=https://YOUR-FRONTEND-URL.up.railway.app
```

3. Railway runs the `railway.toml` build command automatically:
   ```
   npm install && npx prisma generate && npx prisma db push
   ```

4. After the first deploy, seed the database via the Railway shell or CLI:
   ```bash
   node prisma/seed.js
   ```

---

### Step 4 — Deploy the Frontend

1. **+ New** → **GitHub Repo** → select your repository → set root directory to `/frontend`
2. Go to the **Variables** tab and add:

```
VITE_API_URL=https://YOUR-BACKEND-URL.up.railway.app
```

3. Railway will build with `npm install && npm run build` and serve with `npm run preview`

---

### Step 5 — Fix CORS

Go back to your **Backend service → Variables** and update:
```
FRONTEND_URL=https://YOUR-ACTUAL-FRONTEND-URL.up.railway.app
```

Trigger a backend redeploy. Your app is now live. ✅

---

## 🔌 REST API Reference

All protected endpoints require:
```
Authorization: Bearer <token>
```

### Auth
```
POST /api/auth/register    Body: { name, email, password, role }
POST /api/auth/login       Body: { email, password }
```

### Users
```
GET  /api/users            → All workspace users (auth required)
GET  /api/users/me         → Current authenticated user's profile
```

### Projects
```
GET    /api/projects         → Projects the user is a member of
POST   /api/projects         → Create a project (Admin only)
PATCH  /api/projects/:id     → Update a project (Admin + owner)
DELETE /api/projects/:id     → Delete a project (Admin + owner, cascades tasks)
```

### Tasks
```
GET    /api/tasks            → Tasks in user's projects
POST   /api/tasks            → Create a task (Admin only)
PUT    /api/tasks/:id        → Update task (Admin: all fields; Member: status only)
DELETE /api/tasks/:id        → Delete a task (Admin only)
```

---

## 🧪 Testing the API Manually

```bash
# Register a new user
curl -X POST http://localhost:3001/api/auth/register \
  -H "Content-Type: application/json" \
  -d '{"name":"Test User","email":"test@test.com","password":"test123","role":"Admin"}'

# Login and get a JWT token
curl -X POST http://localhost:3001/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"admin@demo.com","password":"admin123"}'

# Use the token to fetch projects
curl http://localhost:3001/api/projects \
  -H "Authorization: Bearer YOUR_TOKEN_HERE"

# Health check
curl http://localhost:3001/health
```

---

## 👤 Author

Built for the Candidate Nomination Assessment — Full Stack Assignment.

- **GitHub**: [github.com/YOUR_USERNAME](https://github.com/YOUR_USERNAME)
- **Live Demo**: [taskflow-frontend.up.railway.app](https://taskflow-frontend.up.railway.app)

---

## 📄 License

This project is licensed under the [MIT License](LICENSE).
