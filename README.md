# devops-node-app
🛠️ **CI/CD Pipeline Automation with Docker, Jenkins & Kubernetes**

This project demonstrates the implementation of a full CI/CD pipeline using **Jenkins**, **Docker**, and **Kubernetes**. The goal is to automate the build, test, and deployment of a Node.js application, ensuring faster and reliable releases with minimal manual intervention.

---

## 🚀 Features
- Automated **CI/CD** pipeline with **Jenkins**
- **Dockerized Node.js application**
- Automated **Docker image push** to **Docker Hub**
- **Kubernetes deployment** with **rolling updates**
- **GitHub integration** for trigger-based builds
- **Deployed on AWS EC2** for scalable infrastructure

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
Clone the repository to your local machine:
```bash
git clone https://github.com/madanneeraj/devops-node-app.git
cd devops-node-app
2.
Build and Run Locally with Docker
docker build -t devops-node-app .
Run the application in a Docker container:
docker run -p 3000:3000 devops-node-app
3. Jenkins Pipeline Setup
Install Jenkins and configure it to run pipelines.

Set up a GitHub webhook for triggering builds when changes are pushed.

Create a Jenkins pipeline job and link it to this repository. Use the Jenkinsfile provided in this repo.

Add the necessary DockerHub credentials and Kubernetes config (Kubeconfig file) to Jenkins.
4. Docker Hub Login
In your Jenkins pipeline, ensure Docker is logged into your Docker Hub account before pushing the image:
withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
}
5. Kubernetes Deployment
Create Kubernetes Deployment and Service files:

deployment.yaml

service.yaml

Apply the Kubernetes resources:
kubectl apply -f k8s/deployment.yaml
kubectl apply -f k8s/service.yaml
🌐 Accessing the Application
After the application is deployed to Kubernetes, expose the service using a NodePort or LoadBalancer service type in Kubernetes.

For a NodePort, use the following command:
kubectl get svc devops-service
The output will provide the NodePort, allowing you to access the application via the IP:NodePort of your AWS EC2 instance.

🚧 Troubleshooting
Jenkins Build Issues: Make sure you have the necessary Docker, Kubernetes, and GitHub credentials set up in Jenkins.

Docker Image Issues: Ensure the Dockerfile is correctly built and the application runs locally before pushing to DockerHub.

Kubernetes Deployment Issues: Make sure your Kubeconfig is correct, and the cluster is running properly.

📈 Future Improvements
CI/CD pipeline enhancements for better error handling and notifications.

Multi-environment deployments (e.g., staging, production).

Auto-scaling for Kubernetes clusters.

Helm charts for easier deployment.




