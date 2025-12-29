from fastapi import FastAPI, Depends, HTTPException

from sqlalchemy.orm import Session

from pydantic import BaseModel

from typing import Optional, List



import models

from database import engine, SessionLocal



##### \# Create database tables

models.Base.metadata.create\_all(bind=engine)



app = FastAPI()



##### \# DB session dependency



def get\_db():

&nbsp;   db = SessionLocal()

&nbsp;   try:

&nbsp;       yield db

&nbsp;   finally:

&nbsp;       db.close()



##### \# ----- Schemas -----



class TaskBase(BaseModel):

&nbsp;   title: str

&nbsp;   description: Optional\[str] = None

&nbsp;   completed: bool = False



class TaskCreate(TaskBase):

&nbsp;   pass



class Task(TaskBase):

&nbsp;   id: int

&nbsp;   owner\_id: int

&nbsp;   class Config:

&nbsp;       from\_attributes = True



\# new: TaskUpdate schema for partial updates



class TaskUpdate(BaseModel):

&nbsp;   title: Optional\[str] = None

&nbsp;   description: Optional\[str] = None

&nbsp;   completed: Optional\[bool] = None





class UserBase(BaseModel):

&nbsp;   username: str 

&nbsp;  

class UserCreate(UserBase):

&nbsp;   password: str



class UserOut(UserBase):

&nbsp;   id: int

&nbsp;   tasks: List\[Task] = \[]

&nbsp;   class Config:

&nbsp;       from\_attributes = True



class UserUpdate(BaseModel):

&nbsp;   username: Optional\[str] = None

&nbsp;   password: Optional\[str] = None







\# ----- User Endpoints -----

@app.post("/users", response\_model=UserOut)

def create\_user(user: UserCreate, db: Session = Depends(get\_db)):

&nbsp;   db\_user = db.query(models.User).filter(models.User.username == user.username).first()

&nbsp;   if db\_user:

&nbsp;       raise HTTPException(status\_code=400, detail="Username already registered")



&nbsp;   new\_user = models.User(username=user.username, password=user.password)

&nbsp;   db.add(new\_user)

&nbsp;   db.commit()

&nbsp;   db.refresh(new\_user)

&nbsp;   return new\_user





@app.get("/users/{user\_id}", response\_model=UserOut)

def get\_user(user\_id: int, db: Session = Depends(get\_db)):

&nbsp;   user = db.query(models.User).filter(models.User.id == user\_id).first()

&nbsp;   if not user:

&nbsp;       raise HTTPException(status\_code=404, detail="User not found")

&nbsp;   return user



\#PUT is both PUT and PATCH here in the following method:



@app.put("/users/{user\_id}", response\_model=UserOut)

def update\_user(user\_id: int, user\_update: UserUpdate, db: Session = Depends(get\_db)):

&nbsp;   user = db.query(models.User).filter(models.User.id == user\_id).first()

&nbsp;   if not user:

&nbsp;       raise HTTPException(status\_code=404, detail="User not found")



&nbsp;   # update fields

&nbsp;   if user\_update.username is not None:

&nbsp;       user.username = user\_update.username

&nbsp;   if user\_update.password is not None:

&nbsp;       user.password = user\_update.password



&nbsp;   db.commit()

&nbsp;   db.refresh(user)

&nbsp;   return user





@app.delete("/users/{user\_id}")

def delete\_user(user\_id: int, db: Session = Depends(get\_db)):

&nbsp;   user = db.query(models.User).filter(models.User.id == user\_id).first()

&nbsp;   if not user:

&nbsp;       raise HTTPException(status\_code=404, detail="User not found")

&nbsp;   db.delete(user)

&nbsp;   db.commit()

&nbsp;   return {"message": f"User {user\_id} deleted successfully"}



\# ----- Task Endpoints -----

@app.post("/users/{user\_id}/tasks", response\_model=Task)

def create\_task\_for\_user(user\_id: int, task: TaskCreate, db: Session = Depends(get\_db)):

&nbsp;   db\_user = db.query(models.User).filter(models.User.id == user\_id).first()

&nbsp;   if not db\_user:

&nbsp;       raise HTTPException(status\_code=404, detail="User not found")



&nbsp;   db\_task = models.Task(\*\*task.dict(), owner\_id=user\_id)

&nbsp;   db.add(db\_task)

&nbsp;   db.commit()

&nbsp;   db.refresh(db\_task)

&nbsp;   return db\_task



@app.get("/tasks", response\_model=List\[Task])

def read\_tasks(db: Session = Depends(get\_db)):

&nbsp;   return db.query(models.Task).all()



@app.get("/tasks/{task\_id}", response\_model=Task)

def read\_task(task\_id: int, db: Session = Depends(get\_db)):

&nbsp;   task = db.query(models.Task).filter(models.Task.id == task\_id).first()

&nbsp;   if not task:

&nbsp;       raise HTTPException(status\_code=404, detail="Task not found")

&nbsp;   return task



\#PUT is both PUT and PATCH here

@app.put("/tasks/{task\_id}", response\_model=Task)

def update\_task(task\_id: int, updated\_task: TaskCreate, db: Session = Depends(get\_db)):

&nbsp;   task = db.query(models.Task).filter(models.Task.id == task\_id).first()

&nbsp;   if not task:

&nbsp;       raise HTTPException(status\_code=404, detail="Task not found")

&nbsp;  

&nbsp;   if updated\_task.title is not None:

&nbsp;       task.title = updated\_task.title

&nbsp;   if updated\_task.description is not None:

&nbsp;       task.description = updated\_task.description

&nbsp;   if updated\_task.completed is not None:

&nbsp;       task.completed = updated\_task.completed



&nbsp;   db.commit()

&nbsp;   db.refresh(task)

&nbsp;   return task



@app.delete("/tasks/{task\_id}")

def delete\_task(task\_id: int, db: Session = Depends(get\_db)):

&nbsp;   task = db.query(models.Task).filter(models.Task.id == task\_id).first()

&nbsp;   if not task:

&nbsp;       raise HTTPException(status\_code=404, detail="Task not found")



&nbsp;   db.delete(task)

&nbsp;   db.commit()

&nbsp;   return {"message": "Task deleted successfully"}



