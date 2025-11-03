# Project-Based Python Course: From Zero to a 3-Tier Web API (with FastAPI)

**Goal:** Take a true beginner from the first `print()` to a working **3-tier** web application (Data → Service → API/Presentation) using **FastAPI**. Every concept directly feeds the final project: a **Task Manager REST API** (users and their tasks).

---

## Course Introduction

* **What you’ll build:**

  * **Data Layer (Level 1 & 2):** SQLite + SQLAlchemy models and CRUD.
  * **Service Layer (Level 2):** Business rules (ownership checks, validations).
  * **API/Presentation Layer (Level 3):** FastAPI endpoints, Pydantic schemas, auth, error handling, docs.
* **Why this order works:** Learn Python fundamentals to **represent/transform data**, then **persist and process** it with business logic, then **expose** it via an HTTP API.
* **Environment:** Python 3.11+; use **venv** or **Anaconda**.

  ```bash
  python -m venv .venv
  # macOS/Linux
  source .venv/bin/activate
  # Windows
  .venv\Scripts\activate
  pip install --upgrade pip
  ```
* **Outcome:** Core Python, OOP, files, errors, packages, SQL basics, SQLAlchemy ORM, async basics, **FastAPI**, DI, testing, and simple API auth.

---

# Level 1 — Python Fundamentals

*(Web App focus: “Data Layer Preparation” — representing, validating, and transforming data.)*

### 1) Introduction to Python & Environment

* Interpreter, scripts, REPL, virtual environments.
* **Relevance:** Later, you isolate app deps (FastAPI, SQLAlchemy).

```bash
python -V
```

```python
# hello.py
print("Hello, Data Layer!")
```

### 2) Variables & Data Types

* **int, float, str, bool**
* **Relevance:** IDs (int), prices (float), titles (str), flags (bool).

```python
user_id = 1
price = 19.99
username = "alice"
is_active = True
print(user_id, price, username, is_active)
```

### 3) Operators

* Arithmetic, comparison, logical, assignment
* **Relevance:** Filtering records, toggles.

```python
a, b = 10, 3
print(a + b, a - b, a * b, a / b, a // b, a % b)
print(a > b, a == b)
is_owner, is_admin = True, False
print(is_owner and not is_admin)
```

### 4) Control Flow

* `if/elif/else`, `for`, `while`, `break`, `continue`
* **Relevance:** Conditional logic, iterating query results.

```python
role = "user"
if role == "admin":
    print("Full access")
elif role == "user":
    print("Limited access")

tasks = ["pay bills", "email client", "deploy app"]
for t in tasks:
    if t.startswith("email"):
        continue
    print("Task:", t)

countdown = 3
while countdown > 0:
    print("T-", countdown)
    countdown -= 1
```

### 5) Basic Data Structures

* **lists, tuples, dicts, sets**
* **Relevance:** In-memory records; JSON-like payloads.

```python
task_titles = ["Pay bills", "Email client"]
task_titles.append("Deploy app")

db_config = ("sqlite", "localhost", 5432)

user = {"id": 1, "username": "alice", "active": True}
print(user["username"])

tags = {"work", "urgent", "work"}  # {'work', 'urgent'}
print(tags)
```

### 6) Functions

* Definitions, params, returns, scope, lambda
* **Relevance:** Reusable business rules/formatting.

```python
def full_name(first: str, last: str) -> str:
    return f"{first.strip().title()} {last.strip().title()}"

format_title = lambda s: s.strip().capitalize()

print(full_name("  alice", "WONDERLAND"))
print(format_title("   pay bills   "))
```

### 7) File I/O

* **Relevance:** Config/seed data.

```python
with open("seed_users.txt", "w", encoding="utf-8") as f:
    f.write("alice\nbob\n")

with open("seed_users.txt", "r", encoding="utf-8") as f:
    users = [line.strip() for line in f]
print("Seeded users:", users)
```

### 8) Error Handling

* `try/except/finally`
* **Relevance:** Graceful API errors.

```python
def safe_div(a, b):
    try:
        return a / b
    except ZeroDivisionError:
        return None
    finally:
        pass

print(safe_div(10, 0))  # None
```

### 9) Modules & Packages

* Importing & creating your own
* **Relevance:** App as a package (models, services, routers).

```
my_utils/
  __init__.py
  text.py
```

```python
# my_utils/text.py
def slugify(s: str) -> str:
    return s.strip().lower().replace(" ", "-")
```

```python
# demo_import.py
from my_utils.text import slugify
print(slugify("Hello World"))  # hello-world
```

### 10) Intro to OOP

* Classes, attributes, methods, `__init__`, `self`
* **Relevance:** Model entities (User, Task) before the ORM.

```python
class User:
    def __init__(self, user_id: int, username: str, active: bool = True):
        self.id = user_id
        self.username = username
        self.active = active
    def deactivate(self):
        self.active = False

alice = User(1, "alice")
alice.deactivate()
print(alice.id, alice.username, alice.active)
```

> ✅ **Level-1 wrap-up:** You can represent and transform data, organize code, and handle errors—ready to persist it and enforce rules.

---

# Level 2 — Intermediate Python & Data Persistence

*(Web App focus: “Business Logic / Service Layer” — rules & persistence.)*

### 1) Advanced OOP

* Inheritance, polymorphism, encapsulation, magic methods
* **Relevance:** Extend models, encapsulate behavior.

```python
class BaseModel:
    def __repr__(self):
        return f"<{self.__class__.__name__} {self.__dict__}>"

class User(BaseModel):
    def __init__(self, user_id: int, username: str, active: bool = True):
        self.id = user_id
        self.username = username
        self._active = active

    @property
    def active(self):
        return self._active

    def deactivate(self):
        self._active = False

class Admin(User):
    def deactivate_user(self, user: User):
        user.deactivate()

print(Admin(99, "root"))
```

### 2) Decorators

* **Relevance:** Cross-cutting concerns (auth, logging).

```python
from functools import wraps

def requires_active_user(fn):
    @wraps(fn)
    def wrapper(user, *args, **kwargs):
        if not user.active:
            raise PermissionError("User is inactive")
        return fn(user, *args, **kwargs)
    return wrapper

@requires_active_user
def create_task(user, title: str):
    return {"owner": user.username, "title": title}
```

### 3) Generators & Iterators

* **Relevance:** Stream large result sets.

```python
def iter_task_titles():
    for i in range(1, 1_000_001):
        yield f"Task #{i}"

for t in iter_task_titles():
    if t.endswith("#3"):
        print(t)
        break
```

### 4) Context Managers

* **Relevance:** Manage resources (files/DB sessions).

```python
from contextlib import contextmanager

@contextmanager
def temporary_note(path):
    f = open(path, "w", encoding="utf-8")
    try:
        yield f
    finally:
        f.close()

with temporary_note("note.txt") as f:
    f.write("Hello context manager")
```

### 5) Intro to Databases (SQL)

* **SELECT/INSERT/UPDATE/DELETE** — foundation for persistence.

### 6) `sqlite3` in Python (direct)

* **Relevance:** Minimal DB for local dev/tests.

```python
import sqlite3

conn = sqlite3.connect("app.db")
cur = conn.cursor()
cur.execute("""CREATE TABLE IF NOT EXISTS users(
  id INTEGER PRIMARY KEY,
  username TEXT UNIQUE NOT NULL,
  active INTEGER NOT NULL DEFAULT 1
)""")
cur.execute("INSERT INTO users(username) VALUES (?)", ("alice",))
conn.commit()
cur.execute("SELECT id, username, active FROM users")
print(cur.fetchall())
conn.close()
```

### 7) ORMs with SQLAlchemy (Recommended)

* **Relevance:** Clean models/queries that integrate with FastAPI.

```python
# pip install sqlalchemy
from sqlalchemy import create_engine, Integer, String, ForeignKey
from sqlalchemy.orm import DeclarativeBase, Mapped, mapped_column, relationship, Session

engine = create_engine("sqlite:///app.db", echo=False)

class Base(DeclarativeBase): pass

class User(Base):
    __tablename__ = "users"
    id: Mapped[int] = mapped_column(Integer, primary_key=True, autoincrement=True)
    username: Mapped[str] = mapped_column(String(50), unique=True, nullable=False)
    active: Mapped[bool] = mapped_column(default=True)
    tasks: Mapped[list["Task"]] = relationship(back_populates="owner")

class Task(Base):
    __tablename__ = "tasks"
    id: Mapped[int] = mapped_column(primary_key=True)
    title: Mapped[str] = mapped_column(String(200), nullable=False)
    done: Mapped[bool] = mapped_column(default=False)
    owner_id: Mapped[int] = mapped_column(ForeignKey("users.id"))
    owner: Mapped[User] = relationship(back_populates="tasks")

Base.metadata.create_all(engine)

with Session(engine) as session:
    alice = User(username="alice")
    session.add_all([alice, Task(title="Pay bills", owner=alice)])
    session.commit()
    print([t.title for t in session.query(Task).all()])
```

### 8) Async Basics

* **Relevance:** Understand `async/await` for scalable I/O.

```python
import asyncio

async def fetch_user(user_id: int):
    await asyncio.sleep(0.1)
    return {"id": user_id, "username": "alice"}

asyncio.run(fetch_user(1))
```

### 9) Type Hinting

* **Relevance:** Clear contracts; Pydantic/FastAPI leverage hints.

```python
from typing import Optional, List
def find_titles(q: Optional[str], items: List[str]) -> List[str]:
    return [i for i in items if not q or q.lower() in i.lower()]
```

### 10) Project Structure Basics

* **Relevance:** Separation of concerns.

```
task_manager/
  __init__.py
  db.py
  models.py
  crud.py
  schemas.py
```

```python
# db.py
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker
DATABASE_URL = "sqlite:///app.db"
engine = create_engine(DATABASE_URL, echo=False, future=True)
SessionLocal = sessionmaker(bind=engine, autoflush=False, autocommit=False)
```

```python
# crud.py
from sqlalchemy.orm import Session
from .models import User, Task

def create_user(db: Session, username: str) -> User:
    user = User(username=username)
    db.add(user); db.commit(); db.refresh(user)
    return user
```

> ✅ **Level-2 wrap-up:** You can persist data with SQLAlchemy, write CRUD, and organize modules—ready to expose services over HTTP.

---

# Level 3 — Web API Development with FastAPI

*(Web App focus: “Presentation/API Layer & Integration” — exposing business logic via HTTP.)*

### 0) Install & Run

```bash
pip install fastapi "uvicorn[standard]" sqlalchemy pydantic[email] python-multipart
```

### 1) Web Concepts & REST

* **HTTP methods:** GET/POST/PUT/PATCH/DELETE
* **Resources:** `/users`, `/users/{id}`, `/tasks?owner_id=1`
* **Request/Response:** JSON + status codes

### 2) Project Layout (final)

```
app/
  __init__.py
  core/
    config.py
    security.py
  db/
    __init__.py
    base.py
    session.py
  models/
    __init__.py
    user.py
    task.py
  schemas/
    __init__.py
    user.py
    task.py
  services/
    __init__.py
    users.py
    tasks.py
  api/
    __init__.py
    deps.py
    routes/
      __init__.py
      users.py
      tasks.py
  main.py
```

### 3) Database Session & Models

**`app/db/session.py`**

```python
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker

DATABASE_URL = "sqlite:///./app.db"
engine = create_engine(DATABASE_URL, connect_args={"check_same_thread": False})
SessionLocal = sessionmaker(bind=engine, autoflush=False, autocommit=False)
```

**`app/db/base.py`**

```python
from sqlalchemy.orm import DeclarativeBase
class Base(DeclarativeBase):
    pass
```

**`app/models/user.py`**

```python
from sqlalchemy.orm import Mapped, mapped_column, relationship
from sqlalchemy import String, Integer, Boolean
from app.db.base import Base

class User(Base):
    __tablename__ = "users"
    id: Mapped[int] = mapped_column(Integer, primary_key=True, autoincrement=True)
    username: Mapped[str] = mapped_column(String(50), unique=True, nullable=False)
    active: Mapped[bool] = mapped_column(Boolean, default=True)
    tasks: Mapped[list["Task"]] = relationship("Task", back_populates="owner", cascade="all, delete-orphan")
```

**`app/models/task.py`**

```python
from sqlalchemy.orm import Mapped, mapped_column, relationship
from sqlalchemy import String, Integer, Boolean, ForeignKey
from app.db.base import Base

class Task(Base):
    __tablename__ = "tasks"
    id: Mapped[int] = mapped_column(Integer, primary_key=True, autoincrement=True)
    title: Mapped[str] = mapped_column(String(200), nullable=False)
    done: Mapped[bool] = mapped_column(Boolean, default=False)
    owner_id: Mapped[int] = mapped_column(ForeignKey("users.id", ondelete="CASCADE"), nullable=False)
    owner: Mapped["User"] = relationship("User", back_populates="tasks")
```

### 4) Pydantic Schemas (Request/Response Models)

**`app/schemas/user.py`**

```python
from pydantic import BaseModel, Field

class UserCreate(BaseModel):
    username: str = Field(min_length=3, max_length=50)

class UserOut(BaseModel):
    id: int
    username: str
    active: bool
    class Config:
        from_attributes = True
```

**`app/schemas/task.py`**

```python
from pydantic import BaseModel, Field
from typing import Optional

class TaskCreate(BaseModel):
    title: str = Field(min_length=1, max_length=200)

class TaskUpdate(BaseModel):
    title: Optional[str] = Field(default=None, min_length=1, max_length=200)
    done: Optional[bool] = None

class TaskOut(BaseModel):
    id: int
    title: str
    done: bool
    owner_id: int
    class Config:
        from_attributes = True
```

### 5) Dependency Injection (DB sessions, simple API key auth)

**`app/api/deps.py`**

```python
from typing import Generator
from fastapi import Depends, HTTPException, status, Header
from sqlalchemy.orm import Session
from app.db.session import SessionLocal

API_KEY = "change-me"  # demo only; use env vars in real apps

def get_db() -> Generator[Session, None, None]:
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()

def get_api_key(x_api_key: str | None = Header(default=None)) -> str:
    if x_api_key != API_KEY:
        raise HTTPException(status_code=status.HTTP_401_UNAUTHORIZED, detail="Invalid or missing API key")
    return x_api_key
```

### 6) Services (Business Logic)

**`app/services/users.py`**

```python
from sqlalchemy.orm import Session
from app.models.user import User

def create_user(db: Session, username: str) -> User:
    user = User(username=username)
    db.add(user); db.commit(); db.refresh(user)
    return user

def get_user(db: Session, user_id: int) -> User | None:
    return db.query(User).filter(User.id == user_id).first()

def list_users(db: Session) -> list[User]:
    return db.query(User).all()
```

**`app/services/tasks.py`**

```python
from sqlalchemy.orm import Session
from fastapi import HTTPException, status
from app.models.task import Task
from app.models.user import User

def create_task(db: Session, owner_id: int, title: str) -> Task:
    owner = db.query(User).filter(User.id == owner_id).first()
    if not owner:
        raise HTTPException(status_code=status.HTTP_404_NOT_FOUND, detail="Owner not found")
    task = Task(title=title, owner_id=owner_id)
    db.add(task); db.commit(); db.refresh(task)
    return task

def list_tasks(db: Session, owner_id: int | None = None) -> list[Task]:
    q = db.query(Task)
    if owner_id is not None:
        q = q.filter(Task.owner_id == owner_id)
    return q.all()

def update_task(db: Session, task_id: int, title: str | None = None, done: bool | None = None) -> Task:
    task = db.query(Task).filter(Task.id == task_id).first()
    if not task:
        raise HTTPException(status_code=status.HTTP_404_NOT_FOUND, detail="Task not found")
    if title is not None:
        task.title = title
    if done is not None:
        task.done = done
    db.commit(); db.refresh(task)
    return task

def delete_task(db: Session, task_id: int) -> None:
    task = db.query(Task).filter(Task.id == task_id).first()
    if not task:
        raise HTTPException(status_code=status.HTTP_404_NOT_FOUND, detail="Task not found")
    db.delete(task); db.commit()
```

### 7) Routers (Endpoints)

**`app/api/routes/users.py`**

```python
from fastapi import APIRouter, Depends, HTTPException, status
from sqlalchemy.orm import Session
from app.api.deps import get_db, get_api_key
from app.schemas.user import UserCreate, UserOut
from app.services.users import create_user, list_users, get_user

router = APIRouter(prefix="/users", tags=["users"])

@router.post("", response_model=UserOut, dependencies=[Depends(get_api_key)])
def create_user_route(payload: UserCreate, db: Session = Depends(get_db)):
    return create_user(db, username=payload.username)

@router.get("", response_model=list[UserOut], dependencies=[Depends(get_api_key)])
def list_users_route(db: Session = Depends(get_db)):
    return list_users(db)

@router.get("/{user_id}", response_model=UserOut, dependencies=[Depends(get_api_key)])
def get_user_route(user_id: int, db: Session = Depends(get_db)):
    user = get_user(db, user_id)
    if not user:
        raise HTTPException(status_code=status.HTTP_404_NOT_FOUND, detail="User not found")
    return user
```

**`app/api/routes/tasks.py`**

```python
from fastapi import APIRouter, Depends, Query
from sqlalchemy.orm import Session
from app.api.deps import get_db, get_api_key
from app.schemas.task import TaskCreate, TaskUpdate, TaskOut
from app.services.tasks import create_task, list_tasks, update_task, delete_task

router = APIRouter(prefix="/tasks", tags=["tasks"])

@router.post("", response_model=TaskOut, dependencies=[Depends(get_api_key)])
def create_task_route(payload: TaskCreate, owner_id: int = Query(...), db: Session = Depends(get_db)):
    return create_task(db, owner_id=owner_id, title=payload.title)

@router.get("", response_model=list[TaskOut], dependencies=[Depends(get_api_key)])
def list_tasks_route(owner_id: int | None = Query(default=None), db: Session = Depends(get_db)):
    return list_tasks(db, owner_id=owner_id)

@router.put("/{task_id}", response_model=TaskOut, dependencies=[Depends(get_api_key)])
def update_task_route(task_id: int, payload: TaskUpdate, db: Session = Depends(get_db)):
    return update_task(db, task_id, title=payload.title, done=payload.done)

@router.delete("/{task_id}", dependencies=[Depends(get_api_key)])
def delete_task_route(task_id: int, db: Session = Depends(get_db)):
    delete_task(db, task_id)
    return {"status": "deleted"}
```

### 8) App Assembly & Startup

**`app/main.py`**

```python
from fastapi import FastAPI
from app.db.session import engine
from app.db.base import Base
from app.api.routes import users, tasks

def create_app() -> FastAPI:
    app = FastAPI(title="Task Manager API", version="1.0.0")
    Base.metadata.create_all(bind=engine)  # simple dev approach
    app.include_router(users.router)
    app.include_router(tasks.router)
    return app

app = create_app()
```

**Run:**

```bash
uvicorn app.main:app --reload
```

* **Swagger UI:** [http://127.0.0.1:8000/docs](http://127.0.0.1:8000/docs)
* **Auth header:** `X-API-Key: change-me`

### 9) Error Handling in FastAPI

* Use `HTTPException` (as shown in services) with correct status codes.

### 10) Background Tasks (example)

```python
# In tasks router (example)
from fastapi import BackgroundTasks

def send_new_task_email(username: str, title: str):
    print(f"[Email] To {username}: New task '{title}' created!")

# In create_task_route:
from app.services.users import get_user
@router.post("", response_model=TaskOut, dependencies=[Depends(get_api_key)])
def create_task_route(payload: TaskCreate, owner_id: int = Query(...), db: Session = Depends(get_db), background_tasks: BackgroundTasks = None):
    task = create_task(db, owner_id=owner_id, title=payload.title)
    user = get_user(db, owner_id)
    if background_tasks and user:
        background_tasks.add_task(send_new_task_email, user.username, task.title)
    return task
```

### 11) Optional: Async DB (recommended for prod)

* SQLAlchemy AsyncIO + `asyncpg` (PostgreSQL), `async` routes/services, `async` session dependency.

### 12) Testing FastAPI with TestClient

```bash
pip install httpx pytest
```

**`tests/test_api.py`**

```python
from fastapi.testclient import TestClient
from app.main import app

client = TestClient(app)
headers = {"X-API-Key": "change-me"}

def test_users_and_tasks():
    r = client.post("/users", json={"username": "alice"}, headers=headers)
    assert r.status_code == 200, r.text
    user = r.json()
    uid = user["id"]

    r = client.post(f"/tasks?owner_id={uid}", json={"title": "Pay bills"}, headers=headers)
    assert r.status_code == 200
    task = r.json()
    assert task["title"] == "Pay bills"

    r = client.put(f"/tasks/{task['id']}", json={"done": True}, headers=headers)
    assert r.status_code == 200
    assert r.json()["done"] is True

    r = client.get(f"/tasks?owner_id={uid}", headers=headers)
    assert r.status_code == 200
    assert len(r.json()) >= 1
```

Run:

```bash
pytest -q
```

---

## How Each Topic Maps to the 3-Tier Design

* **Level 1 (Data Preparation):**
  Variables, types, collections → **record representation**.
  Control flow/functions → **transform/validate**.
  Files/modules → **seed/config + organization**.
  OOP → **domain models** (pre-ORM).

* **Level 2 (Persistence & Business Logic):**
  SQL/sqlite3 → **store/query**.
  SQLAlchemy ORM → **persisted domain models**.
  Decorators/context managers → **auth/logging & safe resources**.
  Generators → **efficient processing**.
  Structure → **clear separation** (db/models/services).

* **Level 3 (Presentation/API & Integration):**
  FastAPI + Pydantic → **validated endpoints**.
  DI → **DB sessions & auth**.
  Routers → **modular API**.
  Error handling → **clean HTTP responses**.
  Background tasks → **non-blocking side effects**.
  Testing → **confidence & maintainability**.

---

## Suggested Learning Path

1. **Weeks 1–2 (Level 1):** Python syntax → data structures → functions → files → errors → OOP.
   *Mini-milestone:* Script that loads users from a file, normalizes names, prints a summary.

2. **Weeks 3–4 (Level 2):** SQL basics → sqlite3 → SQLAlchemy models/CRUD → decorators/context managers → project structure.
   *Mini-milestone:* CLI CRUD for users/tasks using SQLAlchemy services.

3. **Weeks 5–6 (Level 3):** FastAPI intro → schemas → DI → routers → integrate CRUD → errors → tests → (optional) background tasks.
   *Final milestone:* **Task Manager API** with Swagger docs.

---

## Run the Final Project

```bash
# From project root with venv activated:
pip install fastapi "uvicorn[standard]" sqlalchemy pydantic[email] python-multipart httpx pytest

uvicorn app.main:app --reload
# Visit: http://127.0.0.1:8000/docs
# Use header: X-API-Key: change-me
```

**Sample `curl`:**

```bash
curl -H "X-API-Key: change-me" -X POST http://127.0.0.1:8000/users \
  -H "Content-Type: application/json" -d '{"username":"alice"}'

curl -H "X-API-Key: change-me" -X POST "http://127.0.0.1:8000/tasks?owner_id=1" \
  -H "Content-Type: application/json" -d '{"title":"Pay bills"}'

curl -H "X-API-Key: change-me" http://127.0.0.1:8000/tasks?owner_id=1
```

---

## Best Practices Recap

* Small, purposeful functions with **type hints**.
* **Pydantic** for request/response validation.
* Keep **ORM models** (DB) separate from **schemas** (API).
* Centralize **DB sessions** via DI.
* **Test** core flows (create user → create task → update/list/delete).
* Use **environment variables** for secrets/settings (replace the demo API key).

---

> **Extension Ideas:** OAuth2 password flow + JWTs, async PostgreSQL with SQLAlchemy AsyncIO, Alembic migrations, CORS for frontend integration, and containerization with Docker.
