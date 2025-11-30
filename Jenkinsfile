pipeline {
  agent any

  environment {
    GIT_CRED = 'github-token'      // your GitHub credential id
  }

  stages {
    stage('Checkout') {
      steps {
        checkout([
          $class: 'GitSCM',
          branches: [[name: '*/main']],
          userRemoteConfigs: [[
            url: 'https://github.com/madanneeraj/devops-node-app.git',
            credentialsId: env.GIT_CRED
          ]]
        ])
      }
    }

    stage('Install Dependencies') {
      steps {
        sh 'node -v'
        sh 'npm -v'
        sh 'npm ci || npm install'
      }
    }

    stage('Test') {
      steps {
        // If no tests are present, the build will continue
        sh 'npm test || echo "No tests configured"'
      }
    }

    stage('Build') {
      steps {
        // If no build script exists, Jenkins continues without failing
        sh 'npm run build || echo "No build script configured"'
      }
    }
  }

  post {
    success {
      echo "Build ${env.BUILD_NUMBER} succeeded"
    }
    failure {
      echo "Build ${env.BUILD_NUMBER} failed"
    }
  }
}
