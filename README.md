from fastapi import FastAPI, HTTPException, Depends
from pydantic import BaseModel
from typing import List
from fastapi.security import HTTPBasic, HTTPBasicCredentials

app = FastAPI()
security = HTTPBasic()

# Simples banco de dados em memória
users = {"sirthanos": "Doceria25"}
tasks = []

class Task(BaseModel):
    id: int
    title: str
    description: str = None

def get_current_username(credentials: HTTPBasicCredentials = Depends(security)):
    correct_password = users.get(credentials.username)
    if not correct_password or correct_password != credentials.password:
        raise HTTPException(status_code=401, detail="Usuário ou senha inválidos")
    return credentials.username

@app.post("/tasks/", response_model=Task)
def create_task(task: Task, username: str = Depends(get_current_username)):
    tasks.append(task)
    return task

@app.get("/tasks/", response_model=List[Task])
def list_tasks(username: str = Depends(get_current_username)):
    return tasks
