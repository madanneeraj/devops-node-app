pipeline {
    agent any

    environment {
        GITHUB_CREDENTIALS = credentials('github-credentials') // GitHub credentials in Jenkinss
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-credentials') // DockerHub credentials in Jenkins
    }

    stages {
       stage('Clone Repository') {
    steps {
        git branch: 'main', url: 'https://github.com/madanneeraj/devops-node-app.git', credentialsId: 'github-credentials'
    }
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
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
            }
        }

        stage('Docker Push') {
            steps {
                sh 'docker push madanneer1995/devops-node-app:latest'
            }
        }

        stage('Deploy') {
            steps {
                sh 'docker run -d -p 3000:3000 --name devops-app madanneer1995/devops-node-app:latest'
            }
        }
    }

    post {
        always {
            sh 'docker logout'
        }
    }
}
