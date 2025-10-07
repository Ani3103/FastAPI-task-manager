# 🧩 FastAPI Task Manager

A Task Management REST API built using FastAPI, SQLAlchemy, and SQLite.
It supports CRUD operations for users and their associated tasks — all viewable and testable through FastAPI’s interactive docs.

## ⚙️ Tech Stack

Framework: FastAPI

Database: SQLite (via SQLAlchemy ORM)

Validation: Pydantic

Docs: Swagger UI (/docs)

## 🚀 Features

👤 User CRUD — create, read, update, and delete users

✅ Task CRUD — tasks are linked to users (One-to-Many relationship)

🔄 Smart Updates — single or multiple field updates using PUT

🌐 Interactive API Docs — auto-generated Swagger UI

## 🧠 How to Run Locally

Clone the repo

Create and activate a virtual environment

Install dependencies from requirements.txt

Run:

uvicorn main:app --reload


Visit → http://127.0.0.1:8000/docs

## 📁 Project Structure
main.py          → API routes
models.py        → SQLAlchemy models
schemas.py       → Pydantic schemas
database.py      → DB connection setup
requirements.txt → Dependencies
test.db          → SQLite database (ignored in git)

## 🔮 Next Steps

Add JWT Authentication

Deploy on Heroku

Write endpoint tests

### 🧾 Notes

This project was built to learn and demonstrate:

FastAPI fundamentals

Database modeling with SQLAlchemy

Clean CRUD API design

Proper API documentation
