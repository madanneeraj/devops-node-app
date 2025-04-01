pipeline {
    agent any

    environment {
        GITHUB_CREDENTIALS = credentials('github-credentials') // GitHub credentials in Jenkins.
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-credentials') // DockerHub credentials in Jenkins
        KUBECONFIG = credentials('kubernetes-cred') // Kubernetes credentials in Jenkins
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/madanneeraj/devops-node-app.git', credentialsId: "${GITHUB_CREDENTIALS}"
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
                script {
                    sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
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
                    sh '''
                    # Create a deployment if not exists or update the existing one
                    kubectl apply -f - <<EOF
                    apiVersion: apps/v1
                    kind: Deployment
                    metadata:
                      name: devops-app
                      namespace: jenkins
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

                    # Expose the deployment as a service
                    kubectl apply -f - <<EOF
                    apiVersion: v1
                    kind: Service
                    metadata:
                      name: devops-service
                      namespace: jenkins
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
