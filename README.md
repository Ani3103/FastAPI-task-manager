##🧩 FastAPI Task Manager API

A secure, multi-user Task Management REST API built with FastAPI.
Supports full CRUD operations for users and tasks, JWT-based authentication, and protected routes — all testable via interactive Swagger docs.

This project was built end-to-end to understand real backend workflows, from database modeling to authentication and API security.

##⚙️ Tech Stack

Framework: FastAPI

Database: SQLite (SQLAlchemy ORM)

Auth: JWT (Bearer Token Authentication)

Security: OAuth2Password flow (login) + HTTP Bearer tokens

Validation: Pydantic

Docs: Swagger UI (/docs)

##🚀 Features
👤 User Management

Create users with hashed passwords

Retrieve, update, and delete users

One-to-many relationship: each user owns multiple tasks

##✅ Task Management

Create, read, update, and delete tasks

Tasks are user-scoped (users can only access their own tasks)

Smart PUT updates allow partial field updates

🔐 Authentication & Authorization

JWT-based login system

Secure password hashing

Bearer token authorization

Protected endpoints using FastAPI security dependencies

Clean Swagger UI with Bearer token input only

🌐 API Documentation

Auto-generated interactive Swagger UI

Auth-enabled testing directly from /docs

🧠 How Authentication Works (Short + Honest)

User logs in via /login with username & password

API returns a JWT access token

Token is passed as:

Authorization: Bearer <token>


Protected endpoints validate the token and identify the current user

🧠 How to Run Locally
git clone <repo-url>
cd task-manager-backend

python -m venv venv
venv\Scripts\activate

pip install -r requirements.txt
uvicorn main:app --reload


Visit:
👉 http://127.0.0.1:8000/docs

📁 Project Structure
task-manager-backend/
│
├── main.py          # FastAPI app, routes, CRUD endpoints
├── auth.py          # JWT creation, password hashing, auth dependencies
├── models.py        # SQLAlchemy models (User, Task)
├── database.py      # Database engine & session setup
├── requirements.txt # Project dependencies
├── test.db          # SQLite database (ignored in git)
├── README.md
└── venv/


Yes, auth is modularized. No, you did not imagine it. You earned that file.

🧾 What This Project Demonstrates

REST API design with FastAPI

Database modeling & relationships using SQLAlchemy

JWT authentication and Bearer token authorization

Secure password handling

API protection & user-scoped access control

Debugging real backend issues (schema, auth, Swagger, DB sync)

Writing production-aligned backend code (not toy demos)

🔮 Next Steps

Replace SQLite with PostgreSQL

Deploy the API (Render / Railway)

Add environment-based configuration

Write automated endpoint tests
