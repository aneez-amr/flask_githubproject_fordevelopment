🚀 DevOps Project 03 – Dockerized Flask App CI/CD to EC2 (GitHub Actions + Docker Hub)

This project demonstrates a complete CI/CD pipeline for deploying a Dockerized Flask application to an AWS EC2 instance using GitHub Actions, Docker Hub, and SSH-based automated deployment.

This is a real DevOps workflow covering containerization, registry push, remote deployment, and automated updates.

🛠️ Tech Stack

Flask (Python)

Docker

Docker Hub (Image Registry)

GitHub Actions (CI/CD Pipeline)

AWS EC2 (Ubuntu Server)

SSH Deployment

Docker Compose

📦 Project Features

✔ Flask app containerized using Docker
✔ Docker image automatically built & pushed to Docker Hub
✔ GitHub Actions pipeline triggered on every commit to main
✔ EC2 instance automatically pulls latest image
✔ Application updated using docker compose up -d
✔ Zero manual deployment steps after first setup
✔ Publicly accessible web app on EC2

📁 Project Structure
devops-project-03-docker-flask-ec2-cicd/
│
├── app.py
├── requirements.txt
├── Dockerfile
├── docker-compose.yml
└── .github/
    └── workflows/
        └── deploy.yml

🧩 How It Works
1. Developer pushes code to GitHub

Commit → Push → GitHub Actions triggers automatically.

2. GitHub Actions performs CI

Builds Docker image

Tags it as <dockerhub-user>/flask-app:latest

Pushes it to Docker Hub

3. GitHub Actions performs CD

SSH into EC2 using private key stored in GitHub Secrets

Copies updated docker-compose.yml file

Pulls latest Docker image

Restarts the container with the updated image

4. EC2 hosts the new version automatically

App is live at:
http://44.222.180.96/

🐳 Dockerfile
FROM python:3.10-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

EXPOSE 5000

CMD ["python", "app.py"]

🧱 docker-compose.yml
version: '3.8'

services:
  flaskapp:
    image: <your-dockerhub-username>/flask-app:latest
    container_name: flask-app
    ports:
      - "80:5000"
    restart: always

⚙️ GitHub Actions Workflow

.github/workflows/deploy.yml

name: Deploy Flask App to EC2

on:
  push:
    branches: ["main"]

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout Code
      uses: actions/checkout@v3

    - name: Login to Docker Hub
      run: echo "${{ secrets.DOCKERHUB_PASSWORD }}" | docker login -u "${{ secrets.DOCKERHUB_USERNAME }}" --password-stdin

    - name: Build Docker Image
      run: docker build -t ${{ secrets.DOCKERHUB_USERNAME }}/flask-app:latest .

    - name: Push Docker Image
      run: docker push ${{ secrets.DOCKERHUB_USERNAME }}/flask-app:latest

    - name: Copy docker-compose to EC2
      run: |
        echo "${{ secrets.EC2_SSH_KEY }}" > key.pem
        chmod 600 key.pem
        scp -o StrictHostKeyChecking=no -i key.pem docker-compose.yml ${{ secrets.EC2_USER }}@${{ secrets.EC2_HOST }}:/home/ubuntu/

    - name: Deploy on EC2
      run: |
        ssh -o StrictHostKeyChecking=no -i key.pem ${{ secrets.EC2_USER }}@${{ secrets.EC2_HOST }} << 'EOF'
        docker compose pull
        docker compose up -d
        EOF

🔐 GitHub Secrets Used
Secret Name	Purpose
DOCKERHUB_USERNAME	Login to Docker Hub
DOCKERHUB_PASSWORD	Docker Hub access token
EC2_HOST	EC2 public IP address
EC2_USER	Linux username (usually ubuntu)
EC2_SSH_KEY	EC2 private key (.pem contents)
🔥 Deployment Flow Diagram

Developer → GitHub Repo → GitHub Actions → Docker Hub → EC2 (SSH) → Docker Compose → Flask App Live

🚀 How to Access the App

Once deployed:
http://44.222.180.96/

📌 Learning Outcomes

How to containerize a Python application

How to push images to a registry

How to build a real CI/CD pipeline

How to automate deployment to EC2

How to use GitHub Actions securely with SSH keys

How to run production workloads with Docker Compose

🏁 Result

A fully automated CI/CD pipeline that deploys your Dockerized Flask application to an EC2 server without any manual steps.
"# flask_githubproject_fordevelopment" 
