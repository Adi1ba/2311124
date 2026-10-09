# LibraryNest

A library management app for students and librarians: discover books, manage book records, and submit and track loan requests. Built with a FastAPI REST API, a React + TypeScript frontend, and MySQL.

> **Status:** planning. The design docs are in [`docs/`](https://github.com/Adi1ba/LibraryNest/tree/main/docs); the app code is still being developed from the starter template.

## Features

**MVP** (demo before mid-term)

* Create, list, view, update and delete book records through REST APIs
* Search and browse books
* Submit and manage book loan requests
* View and track loan request status
* Input validation with clear error responses
* Interactive API docs at `/docs`

**Beta** (demo before final)

* Sign up and log in with secure authentication
* User profiles: view and update profile information
* Roles: `student`, `librarian` and `admin`
* Role-Based Access Control (RBAC): restrict operations according to user roles and permissions
* Librarian operations: manage books and handle loan requests
* Admin operations: manage users and administrative access
* Security: password hashing, protected endpoints and permission checks

## Tech stack

| **Part**    | **Choice**                                |
| ----------- | ----------------------------------------- |
| Backend     | Python, FastAPI                           |
| Database    | MySQL                                     |
| Frontend    | React + TypeScript + Vite                 |
| API         | RESTful APIs                              |
| Beta extras | JWT authentication, pwdlib (Argon2), RBAC |

## Documentation

| **Doc**                                                                                                     | **What it covers**                                                              |
| ----------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------- |
| [PRD — Product Requirements Document](https://github.com/Adi1ba/LibraryNest/blob/main/docs/01-prd.md)       | Goals, features, user stories and milestones                                    |
| [SRS — Software Requirements Specification](https://github.com/Adi1ba/LibraryNest/blob/main/docs/02-srs.md) | Functional and non-functional requirements, permissions and acceptance criteria |
| [TDD — Technical Design Document](https://github.com/Adi1ba/LibraryNest/blob/main/docs/03-tdd.md)           | Architecture, data model, API endpoints, authentication and security design     |

## Project structure

```text
backend/    FastAPI app and REST API endpoints
frontend/   Vite + React + TypeScript
docs/       PRD, SRS and TDD
```

## Requirements

* [Python](https://www.python.org/downloads/) 3.10+
* [Node.js](https://nodejs.org/) 22+
* [MySQL](https://dev.mysql.com/downloads/)

> On macOS/Linux, use `python3` instead of `python`.

## Run locally

**Backend** (terminal 1):

```bash
cd backend
python -m venv .venv
.venv\Scripts\Activate.ps1
pip install -r requirements.txt
fastapi dev main.py
```

**Frontend** (terminal 2):

```bash
cd frontend
npm install
npm run dev
```

Open the local development URL displayed by Vite, usually http://localhost:5173.

API docs: http://localhost:8000/docs.

Make sure MySQL is running and the database connection is configured before starting the backend.

> Add new Python packages to `backend/requirements.txt`.

## Environment variables

Configure the database connection and application settings using environment variables or a local `.env` file, according to the backend configuration.

| **Variable**              | **Needed for** | **Notes**                                                      |
| ------------------------- | -------------- | -------------------------------------------------------------- |
| MySQL connection settings | MVP            | Configure the database name, host, port, username and password |
| `JWT_SECRET`              | Beta           | Use a long, randomly generated secret for token signing        |
| Authentication settings   | Beta           | Configure token expiry and other security settings as required |

Never commit secrets or `.env` files. Keep local configuration separate from the code repository.

## Deploy to Vercel

1. Push the repository to GitHub.
2. Open [Vercel](https://vercel.com/new) and import the repository.
3. Configure the frontend and backend deployment settings, environment variables and production database connection.

Deployment depends on the repository's Vercel configuration and the hosting requirements of the FastAPI backend and MySQL database.

## Contributing

Every change starts from a GitHub issue. Follow the repository's contribution conventions:

* Create a feature branch for each issue.
* Use clear, descriptive commit messages.
* Open one pull request per issue.
* Include `Closes #issue_number` in the pull request description to link and close the issue after merging.


