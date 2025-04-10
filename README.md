# devops-node-app
# 🛠️ CI/CD Pipeline Automation with Docker, Jenkins & Kubernetes

This project demonstrates the implementation of a full CI/CD pipeline using Jenkins, Docker, and Kubernetes. The goal is to automate the build, test, and deployment of a Node.js application, ensuring faster and reliable releases with minimal manual intervention.

---

## 🚀 Features
- Automated CI/CD pipeline with Jenkins
- Dockerized Node.js application
- Docker image push to Docker Hub
- Kubernetes deployment with rolling updates
- GitHub integration for trigger-based builds
- Deployed on AWS EC2

---

## 🧰 Tech Stack

| Tool       | Purpose                          |
|------------|----------------------------------|
| **Jenkins**     | CI/CD Automation                 |
| **Docker**      | Containerization                 |
| **Kubernetes**  | Container Orchestration          |
| **AWS EC2**     | Hosting Infrastructure           |
| **GitHub**      | Version Control & Webhook Triggers |
| **Node.js**     | Application Backend              |
| **Nginx**       | Reverse Proxy (optional)         |

---

## 📦 Setup Instructions

### 1. Clone the Repository
```bash
git clone https://github.com/yourusername/yourrepo.git
cd yourrepo
2.docker build -t yourapp-name .
3.docker run -p 3000:3000 yourapp-name

4. Jenkins Pipeline Setup
Configure Jenkins with GitHub Webhook

Use the Jenkinsfile provided in this repo

Add DockerHub credentials & Kubernetes config to Jenkins
5.Kubernetes Deployment
kubectl apply -f k8s/deployment.yaml
kubectl apply -f k8s/service.yaml
