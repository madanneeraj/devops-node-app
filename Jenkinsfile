pipeline {
    agent any

    environment {
        GITHUB_CREDENTIALS = 'github-credentials' // GitHub credentials ID in Jenkins...
        DOCKERHUB_CREDENTIALS = 'dockerhub-credentials' // DockerHub credentials ID in Jenkins
        KUBECONFIG = 'kubernetes-cred' // Kubernetes credentials ID in Jenkins (Secret Text)
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
                withCredentials([string(credentialsId: KUBECONFIG, variable: 'KUBE_CONFIG')]) {
                    script {
                        // Write the kubeconfig Secret Text to a temporary file
                        writeFile(file: '/tmp/kubeconfig', text: "${KUBE_CONFIG}")

                        // Set KUBECONFIG to the temporary file
                        sh '''
                        export KUBECONFIG=/tmp/kubeconfig
                        
                        # Create or update the deployment
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
                        
                        # Create or update the service
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
    }

    post {
        always {
            sh 'docker logout'
        }
    }
}
