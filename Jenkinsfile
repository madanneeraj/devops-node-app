pipeline {
    agent any

    environment {
        GITHUB_CREDENTIALS = 'github-credentials' // GitHub credentials ID in Jenkins
        DOCKERHUB_CREDENTIALS = 'dockerhub-credentials' // DockerHub credentials ID in Jenkins
        KUBECONFIG_CONTENT = '''
apiVersion: v1
clusters:
- cluster:
    certificate-authority: /home/ubuntu/.minikube/ca.crt
    extensions:
    - extension:
        last-update: Tue, 01 Apr 2025 17:46:07 UTC
        provider: minikube.sigs.k8s.io
        version: v1.35.0
      name: cluster_info
    server: https://192.168.49.2:8443
  name: minikube
contexts:
- context:
    cluster: minikube
    extensions:
    - extension:
        last-update: Tue, 01 Apr 2025 17:46:07 UTC
        provider: minikube.sigs.k8s.io
        version: v1.35.0
      name: context_info
    namespace: default
    user: minikube
  name: minikube
current-context: minikube
kind: Config
preferences: {}
users:
- name: minikube
  user:
    client-certificate: /home/ubuntu/.minikube/profiles/minikube/client.crt
    client-key: /home/ubuntu/.minikube/profiles/minikube/client.key
'''
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/madanneeraj/devops-node-app.git', credentialsId: GITHUB_CREDENTIALS
            }
        }

        stage('Build') {
            steps {
                sh 'npm install'
            }
        }

        stage('Test') {
            steps {
                sh 'npm test'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t madanneer1995/devops-node-app:latest .'
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(credentialsId: DOCKERHUB_CREDENTIALS, usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                }
            }
        }

        stage('Docker Push') {
            steps {
                sh 'docker push madanneer1995/devops-node-app:latest'
            }
        }

        stage('Kubernetes Deploy') {
    steps {
        script {
            writeFile file: 'kubeconfig', text: env.KUBECONFIG_CONTENT
            sh 'export KUBECONFIG=$(pwd)/kubeconfig'
            sh '''
            kubectl apply -f - <<EOF
apiVersion: apps/v1
kind: Deployment
metadata:
  name: devops-app
  namespace: default
spec:
  replicas: 1
  selector:
    matchLabels:
      app: devops-app
  template:
    metadata:
      labels:
        app: devops-app
    spec:
      containers:
      - name: devops-node-app
        image: madanneer1995/devops-node-app:latest
        ports:
        - containerPort: 3000
EOF

            kubectl apply -f - <<EOF
apiVersion: v1
kind: Service
metadata:
  name: devops-service
  namespace: default
spec:
  selector:
    app: devops-app
  ports:
  - protocol: TCP
    port: 3000
    targetPort: 3000
EOF
            '''
        }
    }
}

    }

    post {
        always {
            sh 'docker logout'
        }
    }
}
