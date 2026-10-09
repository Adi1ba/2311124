# Technical Design Document (TDD) — LibraryNest

| **Field**    | **Value**                                                                                                |
| ------------ | -------------------------------------------------------------------------------------------------------- |
| Product      | LibraryNest                                                                                              |
| Version      | MVP + Beta                                                                                               |
| Releases     | MVP — Mid-term, Beta — Final                                                                             |
| Status       | Draft                                                                                                    |
| Related docs | [Product Requirements Document (PRD)](01-prd.md), [Software Requirements Specification (SRS)](02-srs.md) |

Sections and tables are marked **MVP** or **Beta**. Build the MVP parts for the mid-term; the Beta parts extend them for the final.

## Documents in this set

| **Short form** | **Full form**                       | **Purpose**                                             | **File**    |
| -------------- | ----------------------------------- | ------------------------------------------------------- | ----------- |
| PRD            | Product Requirements Document       | What we build and why                                   | `01-prd.md` |
| SRS            | Software Requirements Specification | Exact requirements, permissions and acceptance criteria | `02-srs.md` |
| TDD            | Technical Design Document           | How we build it: architecture, data model, API          | `03-tdd.md` |

## 1. Overview

* **MVP:** A FastAPI REST API for book management, book search, availability, and loan requests, stored in MySQL and connected to the React + TypeScript frontend.
* **Beta:** The same app gains user registration, login, Student/Librarian/Admin roles, profile management, and role-based access control.

## 2. Tech stack

| **Layer**        | **Choice**         | **Why**                                                         | **Release** |
| ---------------- | ------------------ | --------------------------------------------------------------- | ----------- |
| API              | FastAPI            | Request validation, type hints, and automatic API documentation | MVP         |
| ORM / models     | SQLAlchemy         | Connects Python models and queries to MySQL                     | MVP         |
| Database         | MySQL              | Stores book, loan, and user records                             | MVP         |
| DB driver        | PyMySQL            | Connects Python and MySQL                                       | MVP         |
| Frontend         | React + TypeScript | Builds the library web interface                                | MVP         |
| Authentication   | JWT                | Authenticates users through access tokens                       | Beta        |
| Password hashing | pwdlib with Argon2 | Stores passwords securely                                       | Beta        |

## 3. Architecture

```mermaid
flowchart TD
    U[Student / Librarian / Admin] --> F[React + TypeScript Frontend]
    F -->|REST API / JSON| A[FastAPI Backend]
    A --> O[SQLAlchemy]
    O --> D[(MySQL Database)]
```

### 3.1 Request flow — MVP

```mermaid
sequenceDiagram
    participant U as User
    participant F as React Frontend
    participant A as FastAPI
    participant D as MySQL
    U->>F: Search books or submit loan request
    F->>A: Send HTTP request
    A->>A: Validate request data
    A->>D: Query or update records
    D-->>A: Return database result
    A-->>F: Return JSON response
    F-->>U: Display result
```

### 3.2 Login and authorization flow — Beta

```mermaid
sequenceDiagram
    participant U as User
    participant F as React Frontend
    participant A as FastAPI
    participant D as MySQL
    U->>F: Submit email and password
    F->>A: POST /api/v1/auth/login
    A->>D: Find user and verify password
    D-->>A: Return user record
    A-->>F: Return access token
    F->>A: Request protected resource with token
    A->>A: Verify token and user role
    A->>D: Access permitted data
    D-->>A: Return result
    A-->>F: Return JSON response
```

## 4. Project structure

```text
backend/
  main.py              # Creates FastAPI app and includes routers
  database.py          # MySQL connection and database session
  models.py            # Database models and request/response schemas
  routers/
    books.py           # /api/v1/books
    loans.py           # /api/v1/loans
    auth.py            # (Beta) Registration and login
    users.py           # (Beta) User profile
    admin.py           # (Beta) User and role management
  requirements.txt

frontend/
  src/
    App.tsx
    pages/
      Books.tsx
      Loans.tsx
      Login.tsx        # (Beta)
      Signup.tsx       # (Beta)
      Profile.tsx      # (Beta)
      Admin.tsx        # (Beta)

docs/
  01-prd.md
  02-srs.md
  03-tdd.md
```

Keep routers simple: validate input, check permissions where required, and use the database session to read or write records.

## 5. Data model

### 5.1 `book` table — MVP

| **Column**         | **Type**     | **Rules**                   |
| ------------------ | ------------ | --------------------------- |
| `id`               | integer      | Primary key, auto increment |
| `title`            | varchar(200) | Not null                    |
| `author`           | varchar(150) | Not null                    |
| `isbn`             | varchar(20)  | Unique, not null            |
| `total_copies`     | integer      | Not null, minimum 0         |
| `available_copies` | integer      | Not null, minimum 0         |
| `created_at`       | timestamp    | Not null                    |
| `updated_at`       | timestamp    | Not null                    |

### 5.2 `loan` table — MVP

| **Column**      | **Type**     | **Rules**                                        |
| --------------- | ------------ | ------------------------------------------------ |
| `id`            | integer      | Primary key, auto increment                      |
| `book_id`       | integer      | FK → `book.id`                                   |
| `student_name`  | varchar(100) | Not null                                         |
| `student_email` | varchar(254) | Not null                                         |
| `status`        | varchar(20)  | `Pending`, `Approved`, `Rejected`, or `Returned` |
| `requested_at`  | timestamp    | Not null                                         |
| `returned_at`   | timestamp    | Nullable                                         |

In the MVP, the student's name and email identify the request. Pending requests do not reduce available copies. Approval reduces availability by one, and recording a return increases it by one.

### 5.3 Additional table — Beta

**`app_user`**

| **Column**      | **Type**     | **Rules**                           |
| --------------- | ------------ | ----------------------------------- |
| `id`            | integer      | Primary key, auto increment         |
| `name`          | varchar(100) | Not null                            |
| `email`         | varchar(254) | Unique, not null                    |
| `password_hash` | varchar(255) | Not null; never returned by the API |
| `role`          | varchar(20)  | `Student`, `Librarian`, or `Admin`  |
| `is_active`     | boolean      | Default `true`                      |
| `created_at`    | timestamp    | Not null                            |

**`loan` (additional column in Beta)**

| **Column** | **Type** | **Rules**                                             |
| ---------- | -------- | ----------------------------------------------------- |
| `user_id`  | integer  | FK → `app_user.id`, nullable for existing MVP records |

Indexes: `book(isbn)`, `book(title)`, `loan(book_id)`, `loan(status)`, and `app_user(email)`.

### 5.4 Schemas

| **Schema**      | **Used for**          | **Fields**                                            | **Release** |
| --------------- | --------------------- | ----------------------------------------------------- | ----------- |
| `BookCreate`    | Add a book            | `title`, `author`, `isbn`, `total_copies`             | MVP         |
| `BookUpdate`    | Update a book         | `title?`, `author?`, `isbn?`, `total_copies?`         | MVP         |
| `BookRead`      | Book responses        | Book fields, including `id` and `available_copies`    | MVP         |
| `LoanCreate`    | Submit a loan request | `book_id`, `student_name`, `student_email`            | MVP         |
| `LoanRead`      | Loan responses        | Loan fields, including `id`, `status`, and timestamps | MVP         |
| `SignupRequest` | Register              | `name`, `email`, `password`                           | Beta        |
| `LoginRequest`  | Login                 | `email`, `password`                                   | Beta        |
| `UserRead`      | User responses        | `id`, `name`, `email`, `role`, `is_active`            | Beta        |
| `RoleUpdate`    | Update a role         | `role`                                                | Beta        |
| `ProfileUpdate` | Update a profile      | `name`                                                | Beta        |

## 6. REST API design

Base path: `/api/v1`. Request and response bodies use JSON.

### 6.1 Endpoints — MVP

| **Method** | **Path**                    | **Body**     | **Success**           | **Errors**          | **Purpose**                       |
| ---------- | --------------------------- | ------------ | --------------------- | ------------------- | --------------------------------- |
| `POST`     | `/api/v1/books`             | `BookCreate` | `201 BookRead`        | `409`, `422`        | Add a book                        |
| `GET`      | `/api/v1/books`             | —            | `200 BookRead[]`      | —                   | List and search books             |
| `GET`      | `/api/v1/books/{id}`        | —            | `200 BookRead`        | `404`               | View a book                       |
| `PATCH`    | `/api/v1/books/{id}`        | `BookUpdate` | `200 BookRead`        | `404`, `422`        | Update a book                     |
| `DELETE`   | `/api/v1/books/{id}`        | —            | `204`                 | `404`, `409`        | Delete an eligible book           |
| `POST`     | `/api/v1/loans`             | `LoanCreate` | `201 LoanRead`        | `404`, `422`        | Submit a loan request             |
| `GET`      | `/api/v1/loans`             | —            | `200 LoanRead[]`      | —                   | List loan requests                |
| `GET`      | `/api/v1/loans/{id}`        | —            | `200 LoanRead`        | `404`               | Track a loan request              |
| `PATCH`    | `/api/v1/loans/{id}/status` | `status`     | `200 LoanRead`        | `404`, `409`, `422` | Approve, reject, or record return |
| `GET`      | `/api/health`               | —            | `200 {"status":"ok"}` | —                   | Check API status                  |

The book list endpoint supports an optional `search` query parameter to search by title, author, or ISBN.

### 6.2 Endpoints — Beta

Protected endpoints require `Authorization: Bearer <access_token>`.

**Authentication and profile**

| **Method** | **Path**                 | **Body**        | **Success**        | **Errors**   |
| ---------- | ------------------------ | --------------- | ------------------ | ------------ |
| `POST`     | `/api/v1/auth/signup`    | `SignupRequest` | `201 UserRead`     | `409`, `422` |
| `POST`     | `/api/v1/auth/login`     | `LoginRequest`  | `200` access token | `401`, `422` |
| `GET`      | `/api/v1/users/me`       | —               | `200 UserRead`     | `401`        |
| `PATCH`    | `/api/v1/users/me`       | `ProfileUpdate` | `200 UserRead`     | `401`, `422` |
| `GET`      | `/api/v1/users/me/loans` | —               | `200 LoanRead[]`   | `401`        |

**User and role management**

| **Method** | **Path**                          | **Who** | **Body**     | **Success**      | **Errors**                 |
| ---------- | --------------------------------- | ------- | ------------ | ---------------- | -------------------------- |
| `GET`      | `/api/v1/admin/users`             | Admin   | —            | `200 UserRead[]` | `401`, `403`               |
| `PATCH`    | `/api/v1/admin/users/{id}/role`   | Admin   | `RoleUpdate` | `200 UserRead`   | `401`, `403`, `404`, `422` |
| `PATCH`    | `/api/v1/admin/users/{id}/status` | Admin   | `is_active`  | `200 UserRead`   | `401`, `403`, `404`        |

In Beta, book management and loan processing are restricted to Librarian and Admin roles. Students can search books, submit requests, and view their own loans.

### 6.3 Examples

**Search books**

```http
GET /api/v1/books?search=database
```

```json
[
  {
    "id": 1,
    "title": "Database Systems",
    "author": "Example Author",
    "isbn": "9781234567890",
    "total_copies": 5,
    "available_copies": 3
  }
]
```

**Submit a loan request — MVP**

```http
POST /api/v1/loans
Content-Type: application/json
```

```json
{
  "book_id": 1,
  "student_name": "Student One",
  "student_email": "student@example.com"
}
```

**Response**

```json
{
  "id": 1,
  "book_id": 1,
  "student_name": "Student One",
  "student_email": "student@example.com",
  "status": "Pending"
}
```

### 6.4 Error format

Use FastAPI's standard error format.

```json
{
  "detail": "Loan request not found"
}
```

Use `422` for invalid input, `404` for missing records, `401` for unauthenticated requests, and `403` for unauthorized actions.

### 6.5 Design rules

* Use plural resource names such as `/books`, `/loans`, and `/users`.
* Use `GET` to read, `POST` to create, `PATCH` to update, and `DELETE` to delete.
* Validate request data before database changes.
* Return clear error messages when an operation fails.
* Use `/api/v1` as the API base path.

## 7. Database access

* `database.py` creates the SQLAlchemy engine using the MySQL connection settings.
* A database session is provided to each FastAPI request.
* MVP tables are created in the MySQL database for local development.
* Database changes must preserve book quantities and loan status consistency.
* All database access goes through the FastAPI backend; the frontend does not connect directly to MySQL.

## 8. Configuration

| **Variable**   | **Purpose**                       | **Release** |
| -------------- | --------------------------------- | ----------- |
| `DATABASE_URL` | MySQL connection URL              | MVP         |
| `JWT_SECRET`   | Secret used to sign access tokens | Beta        |

Example local configuration:

```env
DATABASE_URL=mysql+pymysql://username:password@localhost:3306/librarynest
JWT_SECRET=replace_with_a_long_random_secret
```

Do not commit `.env` or database credentials to GitHub.

## 9. Deployment

* The frontend and backend must be configured to communicate through the REST API.
* MySQL must be available in the deployment environment.
* Environment variables must be configured outside the source code.
* The deployed application must be tested for book search, loan requests, and role permissions.

## 10. Authentication and RBAC design — Beta

### 10.1 Authentication

* Users register with their name, email, and password.
* Email addresses must be unique.
* Passwords are stored as secure hashes, not plain text.
* Login returns an access token after successful authentication.
* Protected requests must include a valid access token.

### 10.2 Role permissions

| **Action**                 | **Student** | **Librarian** | **Admin** |
| -------------------------- | ----------- | ------------- | --------- |
| Search and view books      | Yes         | Yes           | Yes       |
| Submit loan requests       | Yes         | No            | No        |
| View own loans             | Yes         | No            | No        |
| Manage books               | No          | Yes           | Yes       |
| Approve or reject requests | No          | Yes           | Yes       |
| Record returns             | No          | Yes           | Yes       |
| Manage users and roles     | No          | No            | Yes       |

### 10.3 Permission checks

* FastAPI verifies the access token before allowing access to protected endpoints.
* The backend checks the user's role before performing restricted operations.
* Students can only view their own loan records.
* Librarian and Admin users can process loan requests.
* Only Admin users can manage user roles and account status.
* Unauthorized requests return `403`; unauthenticated requests return `401`.

## 11. Security — Beta

| **Area**       | **Design**                             |
| -------------- | -------------------------------------- |
| Passwords      | Secure password hashing                |
| Authentication | JWT access tokens                      |
| Authorization  | Role checks in FastAPI                 |
| Database       | Parameterized SQLAlchemy queries       |
| Validation     | Validate input using request schemas   |
| Data exposure  | Never return password hashes           |
| Configuration  | Store secrets in environment variables |

## 12. Database schema updates — Beta

* Add the `app_user` table.
* Add the nullable `user_id` field to the `loan` table to associate new requests with registered users.
* Keep existing MVP loan records valid.
* Ensure the database schema is updated before deploying code that depends on the new fields.

## 13. Risks

| **Risk**                                       | **Mitigation**                                            |
| ---------------------------------------------- | --------------------------------------------------------- |
| Book availability becomes incorrect            | Update availability when loans are approved and returned. |
| Duplicate ISBN records                         | Enforce a unique constraint on ISBN.                      |
| Unauthorized book or loan operations           | Check roles in FastAPI before restricted actions.         |
| Students access another student's loan records | Restrict loan queries to the authenticated student.       |
| Database connection failure                    | Verify MySQL configuration and display a clear error.     |
| Credentials are exposed                        | Keep `.env` and database credentials out of GitHub.       |
