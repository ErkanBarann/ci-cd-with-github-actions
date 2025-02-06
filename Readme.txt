# CI/CD Pipeline with GitHub Actions

This repository contains a **CI/CD pipeline** using GitHub Actions to automate the deployment of a **Node.js + Express** application with Docker.

## Project Purpose
This project is designed to automate the deployment process for a simple **RESTful API** built with Node.js and Express. The API provides basic functionalities such as:
- **User authentication** (login/signup)
- **CRUD operations** for managing a resource (e.g., tasks, products, or posts)
- **Database integration** (MongoDB or PostgreSQL)

The goal is to provide an **automated workflow** that builds, tests, and deploys the application to a server with minimal manual intervention.

## Features
- **Automated testing** using Jest
- **Docker containerization** for the app
- **Automatic build and push** to Docker Hub
- **Deployment to a remote VPS** using SSH

## Prerequisites
Ensure you have the following set up before using this pipeline:

1. **Docker Hub Account**: Store the Docker images.
2. **GitHub Repository**: Add the provided workflow file.
3. **VPS with Docker installed**: Ensure you have an accessible server.
4. **GitHub Secrets Configuration**:
   - `DOCKER_USERNAME`: Your Docker Hub username.
   - `DOCKER_PASSWORD`: Your Docker Hub password.
   - `SERVER_HOST`: The public IP or domain of your VPS.
   - `SERVER_USER`: The SSH username for the VPS.
   - `SERVER_SSH_KEY`: The private SSH key for deployment.

## Setup Instructions
### 1. Clone the Repository
```bash
 git clone https://github.com/yourusername/your-repo.git
 cd your-repo
```

### 2. Create `.env` File (if needed)
Define necessary environment variables inside `.env`.

### 3. Create a `Dockerfile`
Ensure the repository contains a valid `Dockerfile` for containerizing the application.

### 4. Configure GitHub Actions
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

## Deployment
Once you push changes to the `main` branch, GitHub Actions will:
1. **Run tests** on the application.
2. **Build a Docker image** and push it to Docker Hub.
3. **Deploy the image** to your VPS via SSH.

## Notes
- Make sure to **configure GitHub secrets** before triggering the pipeline.
- Ensure your VPS has **Docker installed and running**.
- Modify **port settings** as per your application requirements.

## Author
**Your Name**  
GitHub: ErkanBarann(https://github.com/ErkanBarann)  
Email:  baranerk49@gmail.com
