# 🚀 CI/CD Pipeline with GitHub Actions

This repository contains a **CI/CD pipeline** using GitHub Actions to automate the deployment of a **Node.js + Express** application with Docker.

---

## 🎯 Project Purpose
This project automates the deployment process for a simple **RESTful API** built with Node.js and Express. The API provides essential functionalities such as:

| ✅ Feature              | 💡 Description |
|------------------------|------------------------------|
| 🔐 **User Authentication** | Login & Signup functionality |
| ✍ **CRUD Operations** | Manage tasks, products, or posts |
| 🗄 **Database Integration** | Supports MongoDB & PostgreSQL |

The goal is to provide an **automated workflow** that builds, tests, and deploys the application to a server with minimal manual intervention.

---

## ✨ Features
| 🚀 Feature                  | 🔥 Description |
|----------------------------|------------------------------|
| 🛠 **Automated Testing** | Runs Jest tests automatically |
| 📦 **Docker Containerization** | Packages the app in a Docker container |
| 📤 **Automatic Build & Push** | Builds & pushes images to Docker Hub |
| 🖥 **Deployment via SSH** | Deploys app to a remote VPS |

---

## ⚙️ Prerequisites
Ensure you have the following set up before using this pipeline:

1️⃣ **Docker Hub Account** – Store Docker images
2️⃣ **GitHub Repository** – Add the provided workflow file
3️⃣ **VPS with Docker Installed** – Ensure you have an accessible server
4️⃣ **GitHub Secrets Configuration**:
   - 🔑 `DOCKER_USERNAME`: Your Docker Hub username
   - 🔑 `DOCKER_PASSWORD`: Your Docker Hub password
   - 🌍 `SERVER_HOST`: Public IP or domain of VPS
   - 👤 `SERVER_USER`: SSH username for VPS
   - 🔑 `SERVER_SSH_KEY`: Private SSH key for deployment

---

## 🛠 Setup Instructions
### 1️⃣ Clone the Repository
```bash
 git clone https://github.com/yourusername/your-repo.git
 cd your-repo
```

### 2️⃣ Create `.env` File (if needed)
Define necessary environment variables inside `.env`.

### 3️⃣ Create a `Dockerfile`
Ensure the repository contains a valid `Dockerfile` for containerizing the application.

### 4️⃣ Configure GitHub Actions
Create `.github/workflows/ci-cd.yml` with the following content:

```yaml
name: CI/CD Pipeline

on:
  push:
    branches:
      - main
  pull_request:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v3

      - name: Set up Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'

      - name: Install dependencies
        run: npm install

      - name: Run tests
        run: npm test

  docker-build:
    needs: build
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v3

      - name: Log in to DockerHub
        run: echo "${{ secrets.DOCKER_PASSWORD }}" | docker login -u "${{ secrets.DOCKER_USERNAME }}" --password-stdin

      - name: Build and push Docker image
        run: |
          docker build -t ${{ secrets.DOCKER_USERNAME }}/node-app:latest .
          docker push ${{ secrets.DOCKER_USERNAME }}/node-app:latest

  deploy:
    needs: docker-build
    runs-on: ubuntu-latest
    steps:
      - name: SSH into server and deploy
        uses: appleboy/ssh-action@v0.1.7
        with:
          host: ${{ secrets.SERVER_HOST }}
          username: ${{ secrets.SERVER_USER }}
          key: ${{ secrets.SERVER_SSH_KEY }}
          script: |
            docker pull ${{ secrets.DOCKER_USERNAME }}/node-app:latest
            docker stop node-app || true
            docker rm node-app || true
            docker run -d --name node-app -p 3000:3000 ${{ secrets.DOCKER_USERNAME }}/node-app:latest
```

---

## 🚀 Deployment Process
Once you push changes to the `main` branch, GitHub Actions will:

| 🚀 Step | 🎯 Description |
|------------|------------------------------|
| 🔍 **Run Tests** | Runs Jest tests on the application |
| 🏗 **Build Image** | Creates a Docker image |
| 📤 **Push Image** | Uploads image to Docker Hub |
| 🌍 **Deploy App** | Deploys to remote VPS via SSH |

---

## 📌 Notes
- 🔑 Ensure GitHub secrets are configured before running the pipeline.
- 🖥 Your VPS must have **Docker installed and running**.
- ⚙️ Modify **port settings** as per your application requirements.

---

## 📢 Author
👨‍💻 **Erkan Baran**  
🔗 GitHub: [ErkanBarann](https://github.com/ErkanBarann)  
📧 Email: [baranerk49@gmail.com](mailto:baranerk49@gmail.com)