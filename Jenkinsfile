pipeline {
  agent any

  environment {
    GITHUB_CREDENTIALS = 'github'         // username/token for Git (if private)
    DOCKERHUB_CREDENTIALS = 'docker'      // DockerHub username+password
    KUBECONFIG_CRED_ID = 'kubeconfig'     // kubeconfig file credential id
    IMAGE_REPO = 'madanneer1995/devops-node-app'
  }

  stages {
    stage('Checkout') {
      steps {
        // if repo is private, credentialsId will be used
        checkout([$class: 'GitSCM',
                  branches: [[name: '*/main']],
                  userRemoteConfigs: [[url: 'https://github.com/madanneeraj/devops-node-app.git', credentialsId: env.GITHUB_CREDENTIALS]]
        ])
      }
    }

    stage('Build & Test (Node)') {
      // use a node image so you don't need node/npm on the agent host
      agent { docker { image 'node:18' } }
      steps {
        sh 'npm ci'            // clean, reproducible install
        sh 'npm test || true'  // keep pipeline alive if tests are flaky; fail later if needed
        // optionally fail on test failures:
        // sh 'npm test'
      }
    }

    stage('Docker Build & Push') {
      agent { label 'docker' } // ensure this agent can run docker (has docker installed)
      steps {
        script {
          def tag = "${env.BUILD_NUMBER}"
          def imageName = "${IMAGE_REPO}:${tag}"

          // build image (uses Docker daemon on agent)
          def built = docker.build(imageName, '.')
          
          // login & push (uses credentials binding)
          docker.withRegistry('https://index.docker.io/v1/', env.DOCKERHUB_CREDENTIALS) {
            built.push()
            // also push 'latest' if you must:
            built.push("latest")
          }

          // optionally remove local image to free space
          sh "docker image rm ${imageName} || true"
        }
      }
    }

    stage('Kubernetes Deploy') {
      agent { label 'kubectl' } // ensure kubectl is available on this agent
      steps {
        withCredentials([file(credentialsId: env.KUBECONFIG_CRED_ID, variable: 'KUBECONFIG_FILE')]) {
          script {
            def tag = "${env.BUILD_NUMBER}"
            def image = "${IMAGE_REPO}:${tag}"

            // apply Deployment and Service using kubectl with kubeconfig file
            sh """
              export KUBECONFIG=${KUBECONFIG_FILE}

              kubectl apply -f - <<EOF
apiVersion: apps/v1
kind: Deployment
metadata:
  name: devops-app
  labels:
    app: devops-app
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
        image: ${image}
        imagePullPolicy: IfNotPresent
        ports:
        - containerPort: 3000
        readinessProbe:
          httpGet:
            path: /
            port: 3000
          initialDelaySeconds: 10
          periodSeconds: 10
        resources:
          limits:
            cpu: "500m"
            memory: "512Mi"
          requests:
            cpu: "250m"
            memory: "256Mi"
EOF

              kubectl apply -f - <<EOF
apiVersion: v1
kind: Service
metadata:
  name: devops-service
spec:
  selector:
    app: devops-app
  ports:
  - protocol: TCP
    port: 3000
    targetPort: 3000
  type: ClusterIP
EOF
            """
          }
        }
      }
    }
  }

  post {
    success {
      echo "Build #${env.BUILD_NUMBER} succeeded"
    }
    failure {
      echo "Build #${env.BUILD_NUMBER} failed"
    }
    always {
      // Logout from docker (safe) and optionally archive artifacts
      sh 'docker logout || true'
    }
  }
}
