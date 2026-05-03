# Team Task Manager — Spring Boot Backend

A complete REST API rewrite of the original MERN-stack backend, built with **Spring Boot 3**, **Spring Security**, **JPA/Hibernate**, and **PostgreSQL**.

---

## Tech Stack

| Layer | Technology |
|---|---|
| Framework | Spring Boot 3.2 |
| Language | Java 21 |
| Security | Spring Security 6 + JWT (jjwt 0.12) |
| Persistence | Spring Data JPA + Hibernate |
| Database | PostgreSQL (H2 for tests) |
| Build | Maven |
| Utils | Lombok |

---

## Project Structure

```
src/main/java/com/teamtask/
├── TeamTaskManagerApplication.java   ← entry point
├── config/
│   └── SecurityConfig.java           ← CORS, JWT filter chain
├── controller/
│   ├── AuthController.java           ← POST /api/auth/register|login
│   ├── HealthController.java         ← GET /health
│   ├── ProjectController.java        ← /api/projects
│   ├── TaskController.java           ← /api/tasks
│   └── UserController.java           ← /api/users
├── dto/
│   ├── request/                      ← validated inbound payloads
│   └── response/                     ← outbound response shapes
├── entity/
│   ├── User.java
│   ├── Project.java
│   └── Task.java
├── exception/
│   ├── GlobalExceptionHandler.java
│   └── ResourceNotFoundException.java
├── repository/                       ← Spring Data JPA interfaces
├── security/
│   ├── JwtAuthFilter.java            ← Bearer token extractor
│   ├── JwtUtil.java                  ← sign / parse / validate
│   └── UserDetailsServiceImpl.java
└── service/
    ├── AuthService.java
    ├── ProjectService.java
    ├── TaskService.java
    └── UserService.java
```

---

## Getting Started

### Prerequisites
- Java 21+
- Maven 3.9+
- PostgreSQL 15+

### 1. Clone & configure

```bash
git clone <your-repo>
cd team-task-manager-springboot

cp .env.example .env
# edit .env with your DB credentials and a strong JWT_SECRET
```

### 2. Create the database

```sql
CREATE DATABASE teamtask;
```

### 3. Run

```bash
# Pass env vars inline, or export them first
export DATABASE_URL=jdbc:postgresql://localhost:5432/teamtask
export DB_USERNAME=postgres
export DB_PASSWORD=postgres
export JWT_SECRET=your-very-long-random-secret-here

mvn spring-boot:run
```

The server starts on **http://localhost:3001** (same port as the original Node backend).

### 4. Run tests

```bash
mvn test
```

Tests use an in-memory H2 database — no Postgres needed.

---

## Environment Variables

| Variable | Default | Description |
|---|---|---|
| `DATABASE_URL` | `jdbc:postgresql://localhost:5432/teamtask` | JDBC connection URL |
| `DB_USERNAME` | `postgres` | DB username |
| `DB_PASSWORD` | `postgres` | DB password |
| `JWT_SECRET` | *(see .env.example)* | HMAC-SHA key, min 32 chars |
| `JWT_EXPIRATION_MS` | `604800000` | Token TTL (7 days) |
| `FRONTEND_URL` | `http://localhost:5173` | Allowed CORS origin |

---

## API Reference

The API is **100% compatible** with the original Express backend. No changes needed in the React frontend.

### Auth — `/api/auth`

| Method | Path | Auth | Description |
|---|---|---|---|
| POST | `/api/auth/register` | public | Register new user |
| POST | `/api/auth/login` | public | Login, receive JWT |

### Users — `/api/users`

| Method | Path | Auth | Description |
|---|---|---|---|
| GET | `/api/users/me` | Bearer | Current user profile |
| GET | `/api/users` | Bearer | All users (for assignee picker) |

### Projects — `/api/projects`

| Method | Path | Auth | Description |
|---|---|---|---|
| GET | `/api/projects` | Bearer | Projects I'm a member of |
| POST | `/api/projects` | Admin | Create project |
| PATCH | `/api/projects/:id` | Admin + owner | Update project |
| DELETE | `/api/projects/:id` | Admin + owner | Delete project |

### Tasks — `/api/tasks`

| Method | Path | Auth | Description |
|---|---|---|---|
| GET | `/api/tasks` | Bearer | Tasks in my projects |
| POST | `/api/tasks` | Admin | Create task |
| PUT | `/api/tasks/:id` | Bearer | Update task (Admin: full; Member: status only, own tasks) |
| DELETE | `/api/tasks/:id` | Admin | Delete task |

### Health

| Method | Path | Description |
|---|---|---|
| GET | `/health` | `{ "status": "ok", "timestamp": "..." }` |

---

## Request / Response Examples

### Register
```json
POST /api/auth/register
{
  "name": "Alice",
  "email": "alice@example.com",
  "password": "secret123",
  "role": "Admin"
}
```
```json
201 Created
{ "token": "<jwt>", "user": { "id": "...", "name": "Alice", "email": "...", "role": "Admin", "avatar": "A" } }
```

### Create Task (Admin)
```json
POST /api/tasks
Authorization: Bearer <jwt>

{
  "title": "Build login page",
  "projectId": "<uuid>",
  "priority": "High",
  "assigneeId": "<user-uuid>",
  "dueDate": "2025-06-30"
}
```

---

## Differences from the Express backend

| Feature | Express (original) | Spring Boot |
|---|---|---|
| ORM | Prisma | Hibernate / Spring Data JPA |
| IDs | CUID | UUID (equivalent uniqueness) |
| Password hashing | bcryptjs | BCryptPasswordEncoder |
| JWT library | jsonwebtoken | jjwt 0.12 |
| Schema migrations | Prisma migrate | `ddl-auto=update` (dev); use Flyway/Liquibase in prod |
| Validation | Manual checks | Bean Validation (`@Valid`) |

---

## Production Notes

1. Change `spring.jpa.hibernate.ddl-auto` from `update` to `validate` and use **Flyway** or **Liquibase** for migrations.
2. Set a strong random `JWT_SECRET` (32+ chars).
3. Restrict `FRONTEND_URL` to your exact domain.
4. Run behind a reverse proxy (nginx / Caddy) with HTTPS.
