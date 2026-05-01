# ◈ TaskFlow — Team Task Manager (Spring Boot Edition)

> A full-stack, role-based task management application built with **React**, **Spring Boot**, **Spring Security (JWT)**, and **PostgreSQL (JPA/Hibernate)**.

![Java](https://img.shields.io/badge/Java-17%2B-orange?logo=java)
![Spring Boot](https://img.shields.io/badge/SpringBoot-3.x-6DB33F?logo=springboot)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-JPA-4169E1?logo=postgresql)
![License](https://img.shields.io/badge/License-MIT-green)

---

## 🚀 Features

### 🔐 Authentication

* JWT-based login & registration
* Password hashing using BCrypt
* Stateless authentication (no sessions)
* Secure API endpoints with Spring Security

### 🛡 Role-Based Access Control

| Action              | Admin | Member |
| ------------------- | :---: | :----: |
| Create project      |   ✅   |    ❌   |
| Delete project      |   ✅   |    ❌   |
| Create task         |   ✅   |    ❌   |
| Update task status  |   ✅   |    ✅   |
| View assigned tasks |   ✅   |    ✅   |

---

### 📁 Project Management

* Create & manage projects
* Assign team members
* Track project progress
* Cascade delete tasks on project removal

---

### ✅ Task Management

* Create tasks with:

  * Title, description
  * Status (Todo / In Progress / Done)
  * Priority (High / Medium / Low)
  * Due date
* Assign tasks to users
* Filter tasks by project/user/status

---

### 📊 Dashboard

* Task statistics overview
* Overdue task tracking
* Personal task panel
* Project-wise progress

---

### 👥 Team Management

* View all members
* Track task distribution
* Performance insights per user

---

## 🛠 Tech Stack

### Backend

| Technology      | Purpose                        |
| --------------- | ------------------------------ |
| Java 17+        | Programming language           |
| Spring Boot     | Backend framework              |
| Spring Security | Authentication & authorization |
| JWT             | Token-based security           |
| Spring Data JPA | ORM layer                      |
| Hibernate       | Database ORM                   |
| PostgreSQL      | Database                       |
| Lombok          | Boilerplate reduction          |

---

### Frontend

* React 18
* Vite
* Fetch API (JWT-based requests)

---

## 📁 Project Structure

```
taskflow-backend-spring/
│
├── src/main/java/com/taskflow/
│
│   ├── config/            # Security & JWT configuration
│   ├── controller/        # REST Controllers
│   ├── service/           # Business logic
│   ├── repository/        # JPA repositories
│   ├── entity/            # Database entities
│   ├── dto/               # Request/response models
│   ├── exception/         # Global error handling
│   └── TaskflowApplication.java
│
├── src/main/resources/
│   └── application.properties
│
├── pom.xml
```

---

## 🗄 Database Schema

### User

* id (UUID)
* name
* email (unique)
* password
* role (Admin / Member)

### Project

* id (UUID)
* name
* description
* owner (User)
* members (Many-to-Many)

### Task

* id (UUID)
* title
* description
* status
* priority
* dueDate
* project (Many-to-One)
* assignee (User)

---

## ⚙️ Local Setup

### Prerequisites

* Java 17+
* Maven
* PostgreSQL

---

### 1. Clone Repository

```bash
git clone https://github.com/YOUR_USERNAME/taskflow-springboot.git
cd taskflow-backend-spring
```

---

### 2. Configure Database

Update `application.properties`:

```properties
spring.datasource.url=jdbc:postgresql://localhost:5432/taskmanager
spring.datasource.username=postgres
spring.datasource.password=yourpassword

spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true

jwt.secret=your-secret-key
```

---

### 3. Run Application

```bash
mvn clean install
mvn spring-boot:run
```

Server runs at:

```
http://localhost:8080
```

---

## 🔌 API Endpoints

### Auth

```
POST /api/auth/register
POST /api/auth/login
```

### Users

```
GET /api/users
GET /api/users/me
```

### Projects

```
GET /api/projects
POST /api/projects
DELETE /api/projects/{id}
```

### Tasks

```
GET /api/tasks
POST /api/tasks
PUT /api/tasks/{id}
DELETE /api/tasks/{id}
```

---

## 🔐 Authentication Header

```
Authorization: Bearer <JWT_TOKEN>
```

---

## 🧪 Testing API

```bash
curl -X POST http://localhost:8080/api/auth/login \
-H "Content-Type: application/json" \
-d '{"email":"admin@demo.com","password":"admin123"}'
```

---

## 🚀 Deployment

### Build JAR

```bash
mvn clean package
```

### Run

```bash
java -jar target/taskflow.jar
```

---

## 📌 Future Improvements

* WebSocket for real-time updates
* Email notifications
* File attachments
* Activity logs

---

## 👤 Author

* GitHub: https://github.com/Priyanshu-Pundir099/team-management-system

---

## 📄 License

MIT License
