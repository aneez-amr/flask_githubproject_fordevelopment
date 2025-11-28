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

🔐 GitHub Secrets Setup

**Required Secrets:**
| Secret Name | Purpose |
|------------|---------|
| `DOCKERHUB_USERNAME` | Your Docker Hub username |
| `DOCKERHUB_PASSWORD` | Docker Hub access token (or password) |
| `EC2_HOST` | EC2 public IP address |
| `EC2_USER` | Linux username (depends on AMI - see below) |
| `EC2_SSH_KEY` | EC2 private key (.pem contents) |

**How to Find Your EC2 Username:**

The EC2 username depends on the **AMI (Amazon Machine Image)** you used to launch your instance:

| AMI Type | Default Username |
|----------|-----------------|
| **Ubuntu** | `ubuntu` |
| **Amazon Linux 2** | `ec2-user` |
| **Amazon Linux 2023** | `ec2-user` |
| **Debian** | `admin` |
| **CentOS** | `centos` |
| **RHEL (Red Hat)** | `ec2-user` |
| **SUSE** | `ec2-user` |
| **Fedora** | `fedora` |

**Methods to Find Your EC2 Username:**

1. **Check AWS Console:**
   - Go to EC2 Dashboard → Select your instance
   - Look at the **AMI ID** or **AMI name** in the instance details
   - Match it to the table above

2. **Check AMI Documentation:**
   - In EC2 Console, click on the AMI ID link
   - AWS documentation will show the default username

3. **Try Common Usernames:**
   - Most common: `ubuntu` (for Ubuntu Server)
   - Second most common: `ec2-user` (for Amazon Linux)

4. **Test via SSH:**
   ```bash
   # Try with ubuntu (most common for Ubuntu AMIs)
   ssh -i your-key.pem ubuntu@your-ec2-ip
   
   # If that doesn't work, try ec2-user
   ssh -i your-key.pem ec2-user@your-ec2-ip
   ```

**For This Project:**
Since the project mentions "AWS EC2 (Ubuntu Server)", the username is most likely: **`ubuntu`**

**How to Add Docker Hub Secrets to GitHub:**

1. **Navigate to your GitHub repository**
   - Go to your repository on GitHub
   - Click on **Settings** (top menu bar)

2. **Access Secrets and Variables**
   - In the left sidebar, click on **Secrets and variables** → **Actions**

3. **Add DOCKERHUB_USERNAME:**
   - Click **New repository secret**
   - Name: `DOCKERHUB_USERNAME`
   - Value: Your Docker Hub username (e.g., `yourusername`)
   - Click **Add secret**

4. **Add DOCKERHUB_PASSWORD:**
   - Click **New repository secret** again
   - Name: `DOCKERHUB_PASSWORD`
   - Value: Your Docker Hub password or access token
     - **Note:** For better security, use a Docker Hub Access Token instead of your password
     - To create a token: Docker Hub → Account Settings → Security → New Access Token
   - Click **Add secret**

5. **Verify Secrets:**
   - You should now see both `DOCKERHUB_USERNAME` and `DOCKERHUB_PASSWORD` in your secrets list
   - Secrets are encrypted and only visible when adding/editing (not after saving)

**Quick Access Path:**
```
Repository → Settings → Secrets and variables → Actions → New repository secret
```
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
