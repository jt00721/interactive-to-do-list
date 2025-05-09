# interactive-to-do-list
> A simple, authenticated to-do application with a Go backend, following Clean Architecture and TDD where appropriate.

The Interactive To-Do List web app will allow users to manage tasks through a full-stack application featuring user authentication, real-time CRUD operations, and an engaging UI.

## Core Features
- User registration and login.
- CRUD operations for to-do tasks (create, read, update, delete).
- Real-time task updates
- Optional: Task categorization and due dates.

---

## 📋 Table of Contents

1. [Project Overview](#project-overview)  
2. [Tech Stack](#tech-stack)  
3. [Architecture & Layers](#architecture--layers)  
4. [Features & API Endpoints](#features--api-endpoints)  
5. [Data Model](#data-model)  
6. [Milestones & Timeline](#milestones--timeline)  
7. [Development Workflow](#development-workflow)  
8. [Quiz Yourself!](#quiz-yourself)  

---

## 1. Project Overview

**Goal:** Build a secure, multi-user to-do list service that lets users register, log in, and perform full CRUD on their own tasks.  
**Learning Focus:**
- JWT-based authentication  
- Clean Architecture (handlers, services/use-cases, repositories)  
- Test-Driven Development on core features  
- Table-driven unit tests + one end-to-end integration test  

---

## 2. Tech Stack

- **Language:** Go (1.20+)  
- **Web Framework:** `net/http` + Gorilla Mux (or chi)  
- **Auth:** JWT (`github.com/golang-jwt/jwt`)  
- **Database:** PostgreSQL (via `github.com/jackc/pgx`)  
- **Testing:** Go’s `testing` package + Testify  
- **Logging:** Zap or Logrus  
- **Dependency Management:** Go modules  
- **Repo Hosting:** GitHub  

---

## 3. Architecture & Layers

```text
┌─────────┐      ┌────────────┐      ┌───────────────┐
│  HTTP   │─┐   │  Services  │─┐   │  Repositories │
│ Handlers│ └──▶│ / Use-Cases │ └──▶│ (PostgreSQL)  │
└─────────┘      └────────────┘      └───────────────┘
         ▲                             
         │                              
     Middleware                         
  (Logging, Auth)   
```

- **Handlers:** Decode HTTP, call services, encode responses.  
- **Services/Use-Cases:** Orchestrate business logic, validation, error wrapping.  
- **Repositories:** Database interactions only.  
- **Middleware:** JWT validation, request logging.  

---

## 4. Features & API Endpoints

| Feature                | Method | Path                    | Auth? |
|------------------------|--------|-------------------------|-------|
| Register user          | POST   | `/api/v1/users`         | No    |
| Login                  | POST   | `/api/v1/login`         | No    |
| Create to-do           | POST   | `/api/v1/todos`         | Yes   |
| List user’s to-dos     | GET    | `/api/v1/todos`         | Yes   |
| Get a single to-do     | GET    | `/api/v1/todos/{id}`    | Yes   |
| Update to-do           | PUT    | `/api/v1/todos/{id}`    | Yes   |
| Delete to-do           | DELETE | `/api/v1/todos/{id}`    | Yes   |

---

## 5. Data Model

```sql
-- users table
CREATE TABLE users (
  id         UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  email      TEXT UNIQUE NOT NULL,
  password   TEXT NOT NULL,  -- hashed
  created_at TIMESTAMP WITH TIME ZONE DEFAULT now()
);

-- todos table
CREATE TABLE todos (
  id         UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id    UUID REFERENCES users(id) ON DELETE CASCADE,
  title      TEXT NOT NULL,
  completed  BOOLEAN DEFAULT FALSE,
  due_date   TIMESTAMP WITH TIME ZONE,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT now(),
  updated_at TIMESTAMP WITH TIME ZONE DEFAULT now()
);
```

## 6. Milestones & Timeline

| Days     | Milestone                          | Deliverable                                                      |
|----------|------------------------------------|------------------------------------------------------------------|
| Day 1–2  | Auth scaffolding & tests           | Failing tests for register/login; passing implementation.        |
| Day 3–4  | To-do CRUD use-cases & unit tests  | Table-driven tests for create/read/update/delete.                |
| Day 5    | JWT middleware & protected routes  | Middleware + tests for unauthorized access.                      |
| Day 6–7  | Integration test & cleanup         | End-to-end test; polish error handling & structured logging.     |

---

## 7. Development Workflow

1. **Repo Setup**  
   - `git init`  
   - `go mod init github.com/you/todo-api`  
   - Create `.gitignore`, `README.md`, and basic project directories

2. **Branching**  
   - Use feature branches, e.g., `feat/auth`, `feat/todo-crud`

3. **TDD Cycle**  
   - For each sub-feature, follow:  
     1. **Red** – write a failing test  
     2. **Green** – implement the minimal code to pass  
     3. **Refactor** – clean up code and tests

4. **Code Reviews**  
   - Self-review against the Clean Architecture checklist before each merge

5. **CI (Optional)**  
   - Configure GitHub Actions to run tests and linters on pull requests  


## Old Project Breakdown
### Define Scope & Set Up Project
Goals:

  Define core features and user flows for the Interactive To-Do List.
  Set up the project structure and initialize dependencies.

Tasks:

  Outline the core features:
  User registration and login.
  CRUD operations for to-do tasks (create, read, update, delete).
  Real-time task updates and UI interactions.
  Optional: Task categorization and due dates.
  
  Sketch a UI wireframe showing:
  A dashboard with the to-do list.
  Login/sign-up forms.
  A task management interface.
  Create the project folder structure:
  go
  Copy
  /interactive-todo
    ├── /templates        // HTML templates
    ├── /static           // CSS/JS files
    ├── main.go
    ├── routes.go
    ├── models.go
    └── database.go
    
  Run go mod init interactive-todo and install dependencies (Gin, GORM, jwt-go or sessions, godotenv).
  
📌 Deliverable: Project is initialized with a clear scope, wireframe, and folder structure.

### Set Up Database & Models
Goals:

  Design and implement the database schema for users and tasks.

Tasks:

  Define GORM models for:
  User: Fields like ID, Username, Email, Password (hashed).
  Task: Fields such as ID, Title, Description, Completed (boolean), DueDate, UserID (foreign key).
  Write migration functions to create tables in your chosen database (SQLite or PostgreSQL).
  Seed the database with test data (optional) to verify model relationships.
  
📌 Deliverable: A fully structured database schema with working models and sample data.

### Implement User Authentication & Session Management
Goals:

  Create secure endpoints for user registration, login, and session handling.
  
Tasks:

  Develop HTTP routes for:
  POST /register: Allow new users to register.
  POST /login: Authenticate users and return a JWT or set a session cookie.
  Use bcrypt for password hashing.
  Implement middleware to protect routes (e.g., requiring authentication for task management endpoints).
  Test authentication flows using tools like Postman.
📌 Deliverable: A secure user authentication system with either JWT-based or session-based management.

### Build Backend CRUD for To-Do Tasks
Goals:

  Implement CRUD operations for managing tasks via API endpoints.

Tasks:

  Develop HTTP routes for tasks:
  POST /tasks: Create a new task (authenticated).
  GET /tasks: Retrieve all tasks for the logged-in user.
  PUT /tasks/:id: Update a specific task (e.g., mark as completed).
  DELETE /tasks/:id: Delete a task.
  Ensure that all operations are protected by authentication middleware.
  Validate incoming data and handle errors appropriately.
  
📌 Deliverable: Fully functional backend endpoints for task management integrated with user authentication.

### Build the Frontend & Connect to Backend
Goals:

  Create HTML templates for displaying the to-do list and managing tasks.
  Connect the frontend to your backend API.
  
Tasks:

  Develop templates for:
  Dashboard: Display the list of tasks, user info, and a form for adding tasks.
  Authentication Pages: Login and registration forms.
  Use Go’s html/template to render dynamic content.
  Incorporate AJAX (or simple form submissions) for interactive task management.
  Apply basic CSS (or integrate TailwindCSS/Bootstrap) for a polished look.
  
📌 Deliverable: A functional UI that displays tasks and supports user interactions with the backend.

### Testing, Debugging & UI Enhancements
Goals:

  Test the full app workflow and refine UI/UX.

Tasks:

  Manually test all API endpoints and user flows (registration, login, task CRUD).
  Debug issues with authentication, data validation, or UI rendering.
  
  Refine the UI:
  Improve layout, responsiveness, and user feedback messages.
  Optimize form validations and error displays.
  Gather feedback (even self-reflection) on the user experience.

📌 Deliverable: A stable, bug-free Interactive To-Do List with a smooth, responsive interface.

### Final Testing, Documentation & Deployment
Goals:

  Conduct end-to-end testing.
  Finalize documentation.
  Deploy the app online.
  
Tasks:

  Perform comprehensive testing for both functionality and UI.
  Update the documentation (README, API docs, deployment instructions).
  Use godotenv for managing environment variables.
  Deploy the application to a hosting platform like Railway, Heroku, or Fly.io.

📌 Deliverable: The Interactive To-Do List is fully deployed, documented, and ready for your portfolio.
