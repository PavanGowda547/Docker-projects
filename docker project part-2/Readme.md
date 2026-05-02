# 🐳 Containerising a FastAPI App with Docker

A hands-on project to understand how Docker works by containerising a Python FastAPI application — covering the full workflow from writing a Dockerfile to managing containers using real Docker CLI commands.

---

## 🧰 Tech Stack

| Tool | Purpose |
|------|---------|
| **FastAPI** | Python web framework for building APIs |
| **Uvicorn** | ASGI server that runs the FastAPI app inside the container |
| **Docker** | Containerisation platform to package and run the app |
| **Python 3.11-slim** | Lightweight base image used inside the container |

---

## 🏗️ How It Works

```
Your Machine
     ↓
Docker builds an Image (using Dockerfile)
     ↓
Docker runs a Container from that Image
     ↓
FastAPI app starts via Uvicorn on port 80 (inside container)
     ↓
Your browser hits localhost:8080 → mapped to container port 80
     ↓
API responds with JSON
```

---

## 📂 Project Structure

```
fastapi-docker/
├── app/
│   ├── __init__.py
│   └── main.py          ← FastAPI application code
├── requirements.txt     ← Python dependencies
└── Dockerfile           ← Blueprint for the Docker container
```

---

## 📄 Application Code

### `app/main.py`

A simple FastAPI app with two routes to verify everything is wired correctly inside the container:

```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
def read_root():
    return {"status": "success", "message": "FastAPI is running in Docker!"}

@app.get("/items/{item_id}")
def read_item(item_id: int, q: str = None):
    return {"item_id": item_id, "query": q}
```

### `requirements.txt`

```
fastapi
uvicorn
```

---

## 🐳 The Dockerfile — Explained

The Dockerfile is the blueprint that tells Docker exactly how to build the container image.

```dockerfile
# Use the official Python base image (slim = lightweight)
FROM python:3.11-slim

# Set the working directory inside the container
WORKDIR /code

# Copy requirements first — Docker caches this layer
# so it won't reinstall packages unless requirements change
COPY ./requirements.txt /code/requirements.txt

# Install all dependencies
RUN pip install --no-cache-dir --upgrade -r /code/requirements.txt

# Copy the app folder into the container
COPY ./app /code/app

# Start the Uvicorn server
# --host 0.0.0.0 is required to allow traffic from outside the container
CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "80"]
```

> 💡 **Why copy `requirements.txt` before the app code?** Docker builds in layers. By copying and installing dependencies first, Docker caches that layer — so if only your app code changes, Docker skips reinstalling packages and builds much faster.

---

## 🪜 Build and Run

Open your terminal inside the `fastapi-docker/` folder and run the following:

### 1. Build the Docker Image

```bash
docker build -t my-fastapi-app .
```

This reads the Dockerfile and creates an image called `my-fastapi-app`.

### 2. Run the Container

```bash
docker run -d --name fastapi-container -p 8080:80 my-fastapi-app
```

| Flag | What it does |
|------|-------------|
| `-d` | Runs the container in the background (detached mode) |
| `--name fastapi-container` | Gives the container a readable name |
| `-p 8080:80` | Maps port 8080 on your machine to port 80 inside the container |

### 3. Visit the API

Open your browser and go to:

```
http://localhost:8080
```

You should see:

```json
{"status": "success", "message": "FastAPI is running in Docker!"}
```

Try the second route too:

```
http://localhost:8080/items/42?q=hello
```

---

## 🖥️ Docker CLI Commands Used

These are the core Docker commands used to manage and observe the running container:

### Check running containers

```bash
docker ps
```

Shows all **currently running** containers — useful to confirm your container started successfully.

### Check all containers (including stopped ones)

```bash
docker ps -a
```

Shows every container that exists on your machine, whether running or stopped. Helpful for debugging failed starts.

### View container logs

```bash
docker logs fastapi-container
```

Prints the output from inside the container — you can see Uvicorn's startup messages and any incoming request logs here.

### Stop the container

```bash
docker stop fastapi-container
```

Gracefully stops the running container. The container still exists — it is just no longer running.

### Start the container again

```bash
docker start fastapi-container
```

Restarts a stopped container without needing to `docker run` again. All your settings (ports, name) are preserved.

> 💡 **The difference between `stop`/`start` and `run`:** `docker run` creates a brand new container from the image. `docker start` simply restarts an existing, already-created container.

---

## 🔑 Key Concepts Demonstrated

- **Dockerfile authoring** — writing a multi-step build with layer caching in mind
- **Image vs Container** — understanding that an image is the blueprint and a container is the running instance
- **Port mapping** — connecting your local machine's port to a port inside the container
- **Detached mode** — running containers in the background like a real server
- **Container lifecycle management** — using `start`, `stop`, `ps`, `ps -a`, and `logs` to control and observe containers

---

## ⚠️ Notes

- Make sure Docker Desktop is running before executing any `docker` commands
- If port 8080 is already in use on your machine, change the left side of `-p` to any free port e.g. `-p 9090:80`
- The `python:3.11-slim` image is used over the full Python image to keep the container size small — good practice for production-like setups

---

## 👤 Author

Built as part of a Docker and containerisation learning project.