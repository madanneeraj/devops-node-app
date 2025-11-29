pipeline {
  agent any

  environment {
    GIT_CRED = 'github-token'      // your GitHub credential id (username + token)
    DOCKER_CRED = 'dockerhub'      // your DockerHub credential id (username + password)
    IMAGE_REPO = 'madanneer1995/devops-node-app' // change if you want
  }

  stages {
    stage('Checkout') {
      steps {
        // Uses credentials if repo is private
        checkout([$class: 'GitSCM',
                  branches: [[name: '*/main']],
                  userRemoteConfigs: [[url: 'https://github.com/madanneeraj/devops-node-app.git', credentialsId: env.GIT_CRED]]
        ])
      }
    }

    stage('Build & Test') {
      // run inside node container (requires Docker available to agent)
      agent { docker { image 'node:18' } }
      steps {
        sh 'npm ci'
        sh 'npm test'
      }
    }

    stage('Docker Build & Push') {
      // MUST run on an agent that can access the Docker daemon (see checklist below)
      agent { label 'docker' }
      steps {
        script {
          def tag = "${env.BUILD_NUMBER}"
          def fullImage = "${IMAGE_REPO}:${tag}"

          // build image
          def img = docker.build(fullImage, '.')

          // push using DockerHub credentials stored in Jenkins (dockerhub)
          docker.withRegistry('https://index.docker.io/v1/', env.DOCKER_CRED) {
            img.push(tag)
            img.push('latest')
          }

          // cleanup local image to free space
          sh "docker image rm ${fullImage} || true"
        }
      }
    }

    // Optional: add more stages here (deploy to server, notify, etc.)
  }

  post {
    success {
      echo "Build ${env.BUILD_NUMBER} succeeded"
      archiveArtifacts artifacts: 'package*.json', fingerprint: true
    }
    failure {
      echo "Build ${env.BUILD_NUMBER} failed"
    }
    always {
      sh 'docker logout || true'
    }
  }
}
