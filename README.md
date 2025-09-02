# cloudComputing
Cloud Computing Course Fall 2025

Name: Mitali Yadav

Student ID: 801453849

Email: myadav5@charlotte.edu

Hands-on L3 assignment

# Hands-On L3: Docker Multi-Container Microservice

## Overview

This repository contains the files for a multi-container microservice developed as part of the hands-on assignment. The assignment demonstrates the use of Docker to orchestrate a Python Flask web application, a Redis cache, and a PostgreSQL database.

## Project Structure
app.py: The Python Flask web application.

requirements.txt: Defines the Python dependencies for the Flask app.

Dockerfile: Instructions to build the Docker image for the Flask application.

compose.yaml: Defines and runs the multi-container Docker application.

README.md: This file, documenting the project.

## Execution Steps (Windows)
Follow these steps to set up and run the application on a Windows machine.

### Step 1: Install and Verify Docker Desktop
Download and install Docker Desktop for Windows from the official Docker website.

Start Docker Desktop from the Start Menu. If prompted, enable WSL 2 and restart your computer.

Verify the installation by opening a Command Prompt and running the following command:

```
docker --version
```

### Step 2: Set Up the Flask Application
Create a requirements.txt file with the following dependencies:
```
flask
redis
```
Create an app.py file with the Flask application code:
```
import time
import redis
from flask import Flask

app = Flask(__name__)
cache = redis.Redis(host='redis', port=6379)

def get_hit_count():
    retries = 5
    while True:
        try:
            return cache.incr('hits')
        except redis.exceptions.ConnectionError as exc:
            if retries == 0:
                raise exc
            retries -= 1
            time.sleep(0.5)

@app.route('/')
def hello():
    count = get_hit_count()
    return 'Hello World! I have been seen {} times.\n'.format(count)
```
Create a Dockerfile to containerize the Flask application:

```
FROM python:3.7-alpine
WORKDIR /code
ENV FLASK_APP=app.py
ENV FLASK_RUN_HOST=0.0.0.0
RUN apk add --no-cache gcc musl-dev linux-headers
COPY requirements.txt requirements.txt
RUN pip install -r requirements.txt
EXPOSE 5000
COPY . .
CMD ["flask", "run"]
```
### Step 3: Run the Application with Docker Compose
Create a compose.yaml file to define the services (web and redis):
```
version: "3.9"
services:
  web:
    build: .
    ports:
      - "8000:5000"
    depends_on:
      - redis
  redis:
    image: "redis:alpine"
```
Open your terminal in the project directory and run the following command to build and run the application:
```
docker compose up
```
Access the application in your browser at http://localhost:8000.

### Step 4: PostgreSQL Setup (Optional)
You can also run a PostgreSQL container independently.

Pull the PostgreSQL image:
```
docker pull postgres
```
Create and start a PostgreSQL instance:
```
docker run -d -p 5432:5432 --name postgresql -e POSTGRES_PASSWORD=pass12345 postgres
```
To access the container's shell:
```
docker exec -it postgresql bash
```
To interact with psql:
```
psql -d postgres -U postgres
```

## Learning Outcomes

Gained hands-on experience with Docker and containerization.

Learned how to use docker-compose to orchestrate multiple services.

Practiced networking between containers within a multi-container application.

Deployed a simple web application with a caching layer and a database.

Practiced documenting execution steps and project details for future reference.

