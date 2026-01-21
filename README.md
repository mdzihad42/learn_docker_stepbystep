
🐳 Docker শিখতে Step-by-Step Tutorial (Beginner → Client Ready)
STEP 0: Pre-requisites

Computer এ Docker installed থাকতে হবে

Windows / Mac → Docker Desktop

Linux → docker + docker-compose

Terminal / CMD ব্যবহার জানা

Basic Python + Django জানা

Check:

docker --version
docker compose version

STEP 1: Django Project বানাও
django-admin startproject myproject
cd myproject


Folder Structure:

myproject/
├── manage.py
└── myproject/
    ├── __init__.py
    ├── settings.py
    ├── urls.py
    └── wsgi.py

STEP 2: requirements.txt বানাও
Django>=4.2
gunicorn

STEP 3: Dockerfile তৈরি করো

📍 manage.py যেই folder-এ আছে, সেখানেই

FROM python:3.11-slim

ENV PYTHONDONTWRITEBYTECODE=1
ENV PYTHONUNBUFFERED=1

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

EXPOSE 8000

CMD ["gunicorn", "myproject.wsgi:application", "--bind", "0.0.0.0:8000"]


🔹 Gunicorn ব্যবহার করা হলো production-ready server জন্য।

STEP 4: Docker Image Build করো
docker build -t my_django_image .


Check image:

docker images

STEP 5: Run Container locally (Test)
docker run -d -p 8000:8000 --name my_django_container my_django_image


Browser এ দেখো:

http://localhost:8000


Check running container:

docker ps


Stop / Remove:

docker stop my_django_container
docker rm my_django_container

STEP 6: Share Docker Image with Client

Export image:

docker save my_django_image > my_django_image.tar


Send .tar file to client (via Google Drive / WeTransfer / USB).

Client Side Instructions

Client কে যা করতে হবে:

1️⃣ Docker Desktop install

2️⃣ Load image:

docker load < my_django_image.tar


3️⃣ Run container:

docker run -d -p 8000:8000 --name client_django_app my_django_image


4️⃣ Open browser:

http://localhost:8000


5️⃣ Stop container:

docker stop client_django_app
docker rm client_django_app


✅ Client কিছু Python/Django setup জানবে না। সব Docker handle করবে।

STEP 7 (Optional): Docker Compose

Development এ convenience:

version: "3.9"
services:
  web:
    build: .
    ports:
      - "8000:8000"
    volumes:
      - .:/app


Volumes: development-time live code sync

Production: remove volumes → image frozen

🧠 Key Concepts Learned

Dockerfile → project build instructions

docker build → image বানানো

docker run → container চালানো

docker save/load → image share করা

Volumes → development-time live sync (client-এ optional)

🔑 Summary Flow
Developer
  ↓ docker build
Docker Image
  ↓ docker save
.tar file
  ↓
Client
  ↓ docker load
docker run
Browser → Live Django Project
