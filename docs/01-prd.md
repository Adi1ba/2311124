# LibraryNest — Product Requirements Document (PRD)

| **Field**    | **Value**                                                                                            |
| ------------ | ---------------------------------------------------------------------------------------------------- |
| Product      | LibraryNest                                                                                          |
| Version      | MVP + Beta                                                                                           |
| Releases     | MVP — Mid-term, Beta — Final                                                                         |
| Status       | Draft                                                                                                |
| Related docs | [Software Requirements Specification (SRS)](02-srs.md), [Technical Design Document (TDD)](03-tdd.md) |

This document covers **both releases**. Every goal, feature, and user story has a **Release** column to distinguish what will be developed for the mid-term demonstration and what will be added for the final demonstration.

**Documents in this set**

| **Short form** | **Full form**                       | **Purpose**                                                   | **File**         |
| -------------- | ----------------------------------- | ------------------------------------------------------------- | ---------------- |
| PRD            | Product Requirements Document       | What we build and why                                         | `docs/01-prd.md` |
| SRS            | Software Requirements Specification | Functional requirements, permissions, and acceptance criteria | `docs/02-srs.md` |
| TDD            | Technical Design Document           | Architecture, database design, and API implementation         | `docs/03-tdd.md` |

## 0. Release plan

| **Release** | **When** | **Focus**                                                                                                              |
| ----------- | -------- | ---------------------------------------------------------------------------------------------------------------------- |
| **MVP**     | Mid-term | Core library system: book CRUD, book search, availability display, and basic loan request management                   |
| **Beta**    | Final    | User authentication, Student/Librarian/Admin roles, permission enforcement, user management, and security improvements |

## 1. Purpose

LibraryNest is a lightweight library management web application designed to simplify book discovery and loan management for university students and library staff. The project will be developed in two stages:

1. **MVP (mid-term):** A working library management system with book CRUD operations, book search, availability information, and core loan request and status management.
2. **Beta (final):** Extend the MVP with user registration and login, role-based access control (RBAC), protected API endpoints, user management, and improved security.

The system will use React with TypeScript for the frontend, FastAPI with Python for the backend, and MySQL for data storage.

## 2. Problem statement

Manual library processes can make it difficult for students to find available books and track their loan requests. Librarians also need an organized way to maintain the book catalog, review loan requests, and record book returns.

LibraryNest aims to provide a centralized web application that makes book discovery and loan tracking easier while introducing appropriate access controls for library operations.

## 3. Goals

| **ID** | **Goal**                                                                                                         | **Release** |
| ------ | ---------------------------------------------------------------------------------------------------------------- | ----------- |
| G-1    | Provide REST APIs for creating, viewing, updating, and deleting book records.                                    | MVP         |
| G-2    | Store book and loan information in MySQL so that records persist between application restarts.                   | MVP         |
| G-3    | Allow users to search for books by title, author, or ISBN and view availability.                                 | MVP         |
| G-4    | Allow loan requests to be submitted and tracked through recorded statuses.                                       | MVP         |
| G-5    | Provide registration and login so users can access the system through individual accounts.                       | Beta        |
| G-6    | Enforce Student, Librarian, and Admin roles through RBAC and permission checks.                                  | Beta        |
| G-7    | Allow authorized administrators to manage user accounts and roles.                                               | Beta        |
| G-8    | Protect user and library data through authentication, authorization, input validation, and secure configuration. | Beta        |

## 4. Target users

| **User**               | **Need**                                                                                        | **Release** |
| ---------------------- | ----------------------------------------------------------------------------------------------- | ----------- |
| Student / library user | Search for books, check availability, submit loan requests, and track request status.           | MVP         |
| Librarian              | Manage book records, review requests, update loan statuses, and record returns.                 | MVP         |
| Frontend developer     | Use documented REST APIs to connect the frontend to the backend.                                | MVP         |
| Instructor / reviewer  | Inspect the project and verify its functionality through the application and API documentation. | MVP         |
| Registered student     | Log in and view personal loan records and account information.                                  | Beta        |
| Admin                  | Manage users, assign roles, and control access to administrative features.                      | Beta        |

In the **MVP**, the core library operations will be demonstrated without individual user accounts. In the **Beta**, authenticated users will have role-specific permissions, and personal loan records will be associated with the relevant accounts.

## 5. Scope

### In scope — MVP (mid-term)

* Create, list, view, update, and delete book records.
* Search books by title, author, or ISBN.
* Display book availability.
* Submit loan requests and record their statuses.
* Approve or reject loan requests and record book returns through the core library workflow.
* Validate inputs and return clear API error responses.
* Store book and loan records in MySQL.
* Provide interactive API documentation through FastAPI's `/docs` endpoint.
* Run the core application locally for the mid-term demonstration.

### In scope — Beta (final)

* **Authentication:** User registration and login.
* **Authorization:** Role-based access control for Student, Librarian, and Admin.
* **Student access:** View personal loan records and account information.
* **Librarian access:** Manage books and process loan requests and returns.
* **Admin access:** View users, assign roles, and deactivate accounts.
* **Permission enforcement:** Protect restricted API endpoints and prevent unauthorized operations.
* **Security:** Password hashing, input validation, secure secret configuration, and consistent error handling.
* **Integration:** Connect the React + TypeScript frontend with the FastAPI REST APIs.
* **Deployment:** Prepare the application for the final demonstration.

### Out of scope for this semester

* Online payments and fines processing.
* Email and SMS notifications.
* Advanced reservation queues and waitlists.
* AI-based book recommendations or other AI features.
* External university authentication or social login.
* Unnecessary analytics and advanced reporting.
* Automated test suites and CI/CD pipelines unless required by the instructor.

## 6. Features and priority (MoSCoW)

Priorities are release-specific. A Beta Must Have is required for the final demonstration, not for the mid-term.

| **ID** | **Feature**                                                    | **Release** | **Priority** |
| ------ | -------------------------------------------------------------- | ----------- | ------------ |
| F-1    | Create a book record                                           | MVP         | Must         |
| F-2    | List and view book records                                     | MVP         | Must         |
| F-3    | Update and delete book records                                 | MVP         | Must         |
| F-4    | Search books by title, author, or ISBN                         | MVP         | Must         |
| F-5    | Display book availability                                      | MVP         | Must         |
| F-6    | Submit a loan request and record its status                    | MVP         | Must         |
| F-7    | Approve or reject loan requests and record returns             | MVP         | Must         |
| F-8    | Validate inputs and return clear error responses               | MVP         | Must         |
| F-9    | Store book and loan data in MySQL                              | MVP         | Must         |
| F-10   | Provide interactive API documentation                          | MVP         | Should       |
| F-11   | Connect a basic React frontend to the REST API                 | MVP         | Could        |
| F-12   | User registration and login                                    | Beta        | Must         |
| F-13   | Student, Librarian, and Admin roles                            | Beta        | Must         |
| F-14   | Enforce permissions on protected API endpoints                 | Beta        | Must         |
| F-15   | Associate loan records with authenticated users                | Beta        | Must         |
| F-16   | Manage users, assign roles, and deactivate accounts            | Beta        | Should       |
| F-17   | Display a user's personal loan records and account information | Beta        | Should       |
| F-18   | Apply security improvements and prepare deployment             | Beta        | Must         |

## 7. User stories

### MVP

| **ID** | **Story**                                                                                                            | **Feature** | **Release** |
| ------ | -------------------------------------------------------------------------------------------------------------------- | ----------- | ----------- |
| US-01  | As a librarian, I want to add a book so that the library catalog stays updated.                                      | F-1         | MVP         |
| US-02  | As a library user, I want to view the book catalog so that I can discover available books.                           | F-2         | MVP         |
| US-03  | As a librarian, I want to update or delete book records so that the catalog remains accurate.                        | F-3         | MVP         |
| US-04  | As a library user, I want to search by title, author, or ISBN so that I can find a specific book.                    | F-4         | MVP         |
| US-05  | As a library user, I want to see book availability so that I can decide whether to request a book.                   | F-5         | MVP         |
| US-06  | As a library user, I want to submit a loan request so that I can request a book online.                              | F-6         | MVP         |
| US-07  | As a library user, I want to check a request's status so that I know whether it has been approved or rejected.       | F-6         | MVP         |
| US-08  | As a librarian, I want to approve or reject requests and record returns so that loan records remain accurate.        | F-7         | MVP         |
| US-09  | As a developer, I want clear validation errors and API documentation so that I can understand and use the REST APIs. | F-8, F-10   | MVP         |

### Beta

| **ID** | **Story**                                                                                                                 | **Feature** | **Release** |
| ------ | ------------------------------------------------------------------------------------------------------------------------- | ----------- | ----------- |
| US-10  | As a visitor, I want to register an account so that I can access LibraryNest as a registered user.                        | F-12        | Beta        |
| US-11  | As a registered user, I want to log in so that I can access features permitted for my role.                               | F-12        | Beta        |
| US-12  | As a student, I want to see my own loan records so that I can track my library activity.                                  | F-15, F-17  | Beta        |
| US-13  | As a librarian, I want to manage books and process loan requests so that library operations remain organized.             | F-13, F-14  | Beta        |
| US-14  | As an admin, I want to manage users and assign roles so that access is controlled appropriately.                          | F-16        | Beta        |
| US-15  | As an authorized user, I want the system to restrict actions based on my role so that protected operations remain secure. | F-13, F-14  | Beta        |
| US-16  | As an admin, I want to deactivate an account so that access can be removed when necessary.                                | F-16        | Beta        |
| US-17  | As a user, I want my account and loan information to be protected so that unauthorized users cannot access it.            | F-14, F-18  | Beta        |

## 8. Success metrics

### MVP

* All required book CRUD API operations work during the mid-term demonstration.
* Book searches by title, author, and ISBN return the expected matching records.
* Book and loan records persist in MySQL after an application restart.
* 100% of submitted loan requests have a recorded status.
* Book search responses complete within 2 seconds under normal local testing conditions.
* The `/docs` page displays the available API endpoints and their request and response schemas.

### Beta

* Users can register and log in successfully using valid credentials.
* 100% of tested unauthorized role-restricted API requests are denied.
* Students cannot access other users' personal loan records.
* Librarian and Admin operations are restricted according to the approved permission matrix in the SRS.
* Passwords are never stored in plain text.
* The frontend and backend work together for the defined core workflows.
* The application is ready for the final demonstration in the agreed deployment environment.

## 9. Assumptions and constraints

* The project uses React + TypeScript for the frontend, FastAPI + Python for the backend, and MySQL for the database.
* The backend exposes REST APIs and provides interactive API documentation through `/docs`.
* The MVP focuses on core book and loan operations without individual user accounts.
* The Beta extends the MVP with authentication, user-specific records, and role-based permissions.
* Students, Librarians, and Admins have different permissions, defined in the SRS.
* The frontend and backend must integrate within the available semester timeline.
* Deployment configuration will depend on the selected hosting environment and its support for FastAPI and MySQL.
* The feature scope will remain limited to the requirements approved for the mid-term and final demonstrations.

## 10. Milestones

### MVP — Mid-term

| **Milestone**      | **Deliverable**                                                          |
| ------------------ | ------------------------------------------------------------------------ |
| M1 — Documentation | PRD, SRS, and TDD covering both MVP and Beta                             |
| M2 — Data layer    | MySQL database and book/loan data models                                 |
| M3 — Book API      | Book CRUD, search, availability, and input validation                    |
| M4 — Loan API      | Loan requests, status tracking, approval/rejection, and return recording |
| M5 — Mid-term demo | Core library workflows running locally with API documentation            |

### Beta — Final

| **Milestone**                 | **Deliverable**                                                                               |
| ----------------------------- | --------------------------------------------------------------------------------------------- |
| B1 — Authentication           | User registration and login                                                                   |
| B2 — RBAC                     | Student, Librarian, and Admin roles with permission checks                                    |
| B3 — User management          | Admin user management and account deactivation                                                |
| B4 — Integration and security | Frontend-backend integration, password hashing, protected endpoints, and secure configuration |
| B5 — Final demo               | Integrated LibraryNest application prepared for the agreed deployment environment             |

## 11. Release dependencies

* The Beta builds on the MVP and extends the existing book and loan APIs rather than replacing the core functionality.
* The MySQL schema will be extended to include user accounts and the relationships needed to associate loan records with users.
* Authentication must be implemented before user-specific loan records and role-restricted operations can be enforced.
* RBAC and permission checks must be implemented before the Beta is considered complete.
* The frontend depends on stable REST API endpoints and consistent request and response formats.
* Deployment depends on a compatible hosting environment, correct environment variables, and a working MySQL connection.
