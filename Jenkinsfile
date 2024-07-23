pipeline {
  agent {
    docker {
      image 'node:14' // Use the official Node.js Docker image
      args '--user root -v /var/run/docker.sock:/var/run/docker.sock' // Mount Docker socket to access the host's Docker daemon
    }
  }
  stages {
    stage('Checkout') {
      steps {
        sh 'echo passed'
        // git branch: 'main', url: 'https://github.com/uvalentino/to-do-app.git'
      }
    }
    stage('Install Dependencies') {
      steps {
        sh 'npm install'
      }
    }
    stage('Build and Test') {
      steps {
        sh 'npm run build' // Modify if you have a different build script
        sh 'npm test' // Modify if you have a different test script
      }
    }
    stage('Static Code Analysis') {
      environment {
        SONAR_URL = "http://99.79.48.25:9000"
      }
      steps {
        withCredentials([string(credentialsId: 'sonarqube', variable: 'SONAR_AUTH_TOKEN')]) {
          sh 'npx sonar-scanner -Dsonar.login=$SONAR_AUTH_TOKEN -Dsonar.host.url=${SONAR_URL}'
        }
      }
    }
    stage('Build and Push Docker Image') {
      environment {
        DOCKER_IMAGE = "uvalentino/to-do-app:${BUILD_NUMBER}"
        REGISTRY_CREDENTIALS = credentials('docker-cred')
      }
      steps {
        script {
          sh 'docker build -t ${DOCKER_IMAGE} .'
          def dockerImage = docker.image("${DOCKER_IMAGE}")
          docker.withRegistry('https://index.docker.io/v1/', "docker-cred") {
            dockerImage.push()
          }
        }
      }
    }
    stage('Trivy') {
      steps {
        sh "trivy uvalentino/to-do-app:${BUILD_NUMBER}"
      }
    }
    stage('Run Docker Container') {
      steps {
        script {
          def containerName = "to-do-app-container"
          def dockerImage = "uvalentino/to-do-app:${BUILD_NUMBER}"

          // Stop and remove the existing container if it exists
          sh """
            if [ \$(docker ps -q -f name=${containerName}) ]; then
              docker stop ${containerName}
              docker rm ${containerName}
            fi
          """

          // Run the new container
          sh """
            docker run -d --name ${containerName} -p 9090:8080 ${dockerImage} 
          """
        }
      }
    }
  }
}
