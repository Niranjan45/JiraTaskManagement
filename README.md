# Jira-Like Task Management System

Enterprise backend built with **Spring Boot 3**, **PostgreSQL**, and **Spring Data JPA**.

---

## Project Structure

```
jira-task-management/
├── pom.xml
└── src/
    ├── main/
    │   ├── java/com/taskmanager/
    │   │   ├── TaskManagementApplication.java
    │   │   ├── config/
    │   │   │   ├── DataInitializer.java        # Seeds sample data on startup
    │   │   │   └── OpenApiConfig.java          # Swagger/OpenAPI setup
    │   │   ├── controller/
    │   │   │   ├── UserController.java
    │   │   │   ├── ProjectController.java
    │   │   │   └── TaskController.java
    │   │   ├── dto/
    │   │   │   ├── request/
    │   │   │   │   ├── CreateUserRequest.java
    │   │   │   │   ├── UpdateUserRequest.java
    │   │   │   │   ├── CreateProjectRequest.java
    │   │   │   │   ├── UpdateProjectRequest.java
    │   │   │   │   ├── CreateTaskRequest.java
    │   │   │   │   ├── UpdateTaskRequest.java
    │   │   │   │   ├── AddCommentRequest.java
    │   │   │   │   └── TaskFilterRequest.java
    │   │   │   └── response/
    │   │   │       ├── ApiResponse.java        # Generic wrapper
    │   │   │       ├── UserResponse.java
    │   │   │       ├── ProjectResponse.java    # Includes progress %
    │   │   │       ├── TaskResponse.java       # Includes overdue flag
    │   │   │       ├── TaskCommentResponse.java
    │   │   │       ├── TaskActivityResponse.java
    │   │   │       └── DashboardResponse.java
    │   │   ├── entity/
    │   │   │   ├── User.java
    │   │   │   ├── Project.java
    │   │   │   ├── Task.java
    │   │   │   ├── TaskComment.java
    │   │   │   └── TaskActivity.java
    │   │   ├── enums/
    │   │   │   ├── UserRole.java       # ADMIN, PROJECT_MANAGER, DEVELOPER, TESTER, VIEWER
    │   │   │   ├── TaskStatus.java     # OPEN, IN_PROGRESS, BLOCKED, DONE
    │   │   │   ├── TaskPriority.java   # LOW, MEDIUM, HIGH, CRITICAL
    │   │   │   └── ActivityType.java   # TASK_CREATED, STATUS_CHANGED, etc.
    │   │   ├── exception/
    │   │   │   ├── ResourceNotFoundException.java
    │   │   │   ├── DuplicateResourceException.java
    │   │   │   └── GlobalExceptionHandler.java  # @ControllerAdvice
    │   │   ├── mapper/
    │   │   │   ├── UserMapper.java           # MapStruct
    │   │   │   ├── TaskCommentMapper.java
    │   │   │   └── TaskActivityMapper.java
    │   │   ├── repository/
    │   │   │   ├── UserRepository.java
    │   │   │   ├── ProjectRepository.java
    │   │   │   ├── TaskRepository.java        # Rich filtering queries
    │   │   │   ├── TaskCommentRepository.java
    │   │   │   └── TaskActivityRepository.java
    │   │   └── service/
    │   │       ├── UserService.java
    │   │       ├── ProjectService.java
    │   │       ├── TaskService.java
    │   │       └── impl/
    │   │           ├── UserServiceImpl.java
    │   │           ├── ProjectServiceImpl.java
    │   │           └── TaskServiceImpl.java    # Audit trail logic here
    │   └── resources/
    │       └── application.properties
    └── test/
        ├── java/com/taskmanager/service/
        │   ├── UserServiceTest.java
        │   └── TaskServiceTest.java
        └── resources/
            └── application-test.properties    # H2 in-memory for tests
```

---

## Tech Stack

| Layer        | Technology                          |
|-------------|--------------------------------------|
| Framework   | Spring Boot 3.2                      |
| ORM         | Spring Data JPA / Hibernate          |
| Database    | PostgreSQL (H2 for tests)            |
| Mapping     | MapStruct 1.5                        |
| Boilerplate | Lombok                               |
| Docs        | SpringDoc OpenAPI / Swagger UI       |
| Validation  | Jakarta Bean Validation              |
| Logging     | SLF4J + Logback                      |
| Testing     | JUnit 5 + Mockito                    |

---

## Prerequisites

- Java 17+
- Maven 3.8+
- PostgreSQL 14+

---

## Database Setup

```sql
CREATE DATABASE taskmanager_db;
CREATE USER postgres WITH PASSWORD 'postgres';
GRANT ALL PRIVILEGES ON DATABASE taskmanager_db TO postgres;
```

Or with a custom user:
```sql
CREATE USER taskuser WITH PASSWORD 'taskpass';
CREATE DATABASE taskmanager_db OWNER taskuser;
```
Then update `application.properties` accordingly.

---

## Configuration

Edit `src/main/resources/application.properties`:

```properties
spring.datasource.url=jdbc:postgresql://localhost:5432/taskmanager_db
spring.datasource.username=postgres
spring.datasource.password=postgres
```

For **MySQL** instead of PostgreSQL:
1. Uncomment the MySQL dependency in `pom.xml` and remove the PostgreSQL one.
2. Change the datasource URL:
```properties
spring.datasource.url=jdbc:mysql://localhost:3306/taskmanager_db?useSSL=false&serverTimezone=UTC
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL8Dialect
```

---

## Build & Run

```bash
# Clone / extract the project
cd jira-task-management

# Build (skip tests for first run)
mvn clean install -DskipTests

# Run
mvn spring-boot:run
```

The app starts at **http://localhost:8080/api/v1**

Sample data (5 users, 2 projects, 7 tasks) is seeded automatically on first run.

---

## API Documentation

Swagger UI → **http://localhost:8080/api/v1/swagger-ui.html**  
OpenAPI JSON → **http://localhost:8080/api/v1/api-docs**

---

## API Reference

### Users  `/users`

| Method | Endpoint          | Description          |
|--------|-------------------|----------------------|
| POST   | `/users`          | Create user          |
| GET    | `/users`          | List all users       |
| GET    | `/users/{id}`     | Get user by ID       |
| PUT    | `/users/{id}`     | Update user          |
| DELETE | `/users/{id}`     | Delete user          |
| GET    | `/users/search?keyword=` | Search users  |

### Projects  `/projects`

| Method | Endpoint                    | Description               |
|--------|-----------------------------|---------------------------|
| POST   | `/projects`                 | Create project            |
| GET    | `/projects`                 | List all (paginated)      |
| GET    | `/projects/{id}`            | Get project by ID         |
| PUT    | `/projects/{id}`            | Update project            |
| DELETE | `/projects/{id}`            | Delete project            |
| GET    | `/projects/user/{userId}`   | Projects by creator       |
| GET    | `/projects/search?keyword=` | Search projects           |

### Tasks  `/tasks`

| Method | Endpoint                              | Description                     |
|--------|---------------------------------------|---------------------------------|
| POST   | `/tasks`                              | Create task                     |
| GET    | `/tasks/{id}`                         | Get task by ID                  |
| PUT    | `/tasks/{id}`                         | Update task (status, priority…) |
| DELETE | `/tasks/{id}`                         | Delete task                     |
| GET    | `/tasks/project/{projectId}`          | Tasks by project (paginated)    |
| GET    | `/tasks/user/{userId}`                | Tasks assigned to user          |
| GET    | `/tasks/filter`                       | Advanced filter + search        |
| GET    | `/tasks/overdue`                      | All overdue tasks               |
| GET    | `/tasks/overdue/project/{projectId}`  | Overdue tasks by project        |
| POST   | `/tasks/{taskId}/comments`            | Add comment                     |
| GET    | `/tasks/{taskId}/comments`            | List comments                   |
| GET    | `/tasks/{taskId}/activities`          | Audit history                   |
| GET    | `/tasks/dashboard`                    | Global metrics                  |
| GET    | `/tasks/dashboard/project/{id}`       | Per-project metrics             |

### Pagination & Filtering

All list endpoints accept standard Spring pageable params:
```
?page=0&size=10&sort=createdAt,desc
```

Filter endpoint example:
```
GET /tasks/filter?projectId=1&status=IN_PROGRESS&priority=HIGH&keyword=auth&page=0&size=5
```

---

## Sample Request Bodies

### Create User
```json
POST /api/v1/users
{
  "name": "John Doe",
  "email": "john@example.com",
  "role": "DEVELOPER"
}
```

### Create Project
```json
POST /api/v1/projects
{
  "name": "My New Project",
  "description": "Project description here",
  "createdById": 1
}
```

### Create Task
```json
POST /api/v1/tasks
{
  "title": "Implement login API",
  "description": "JWT-based authentication endpoint",
  "priority": "HIGH",
  "projectId": 1,
  "assignedUserId": 2,
  "dueDate": "2025-06-30"
}
```

### Update Task (status change)
```json
PUT /api/v1/tasks/1
{
  "status": "IN_PROGRESS",
  "updatedById": 2
}
```

### Add Comment
```json
POST /api/v1/tasks/1/comments
{
  "userId": 2,
  "commentText": "Started working on this. Should be done by Friday."
}
```

---

## Running Tests

```bash
mvn test
```

Tests use an H2 in-memory database automatically (via `application-test.properties`).

---

## Actuator Health Check

```
GET http://localhost:8080/api/v1/actuator/health
```

---

## Key Design Decisions

- **Audit trail**: Every field change on a Task is recorded in `task_activities` with old/new values and who made the change.
- **Overdue detection**: Computed at query time using `LocalDate.now()` — no scheduled job needed.
- **Progress calculation**: `ProjectResponse` includes `progressPercentage` calculated live from `DONE` tasks / total tasks.
- **Generic API wrapper**: All responses use `ApiResponse<T>` for consistent `success`, `message`, `data`, `timestamp` structure.
- **DTO pattern**: Entities never leak out of the service layer — all controllers receive/return DTOs only.
