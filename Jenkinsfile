pipeline {
    agent {
        kubernetes {
            label 'docker-agent'
            defaultContainer 'docker'
            yaml """
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: docker
    image: docker:24-dind
    securityContext:
      privileged: true
    command:
    - cat
    tty: true
"""
        }
    }

    environment {
        DOCKERHUB_USERNAME = "pinkmelon"
        IMAGE_NAME = "pinkmelon/spring-productt"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Thavornn/17_Yin_Chheng-Thavorn_SR_SPRING_HOMEWORK001'
            }
        }

        stage('Build Docker Image') {
            steps {
                container('docker') {
                    sh '''
                    docker --version
                    docker build -t ${IMAGE_NAME}:${BUILD_NUMBER} .
                    '''
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                container('docker') {
                    withCredentials([string(credentialsId: 'dockerhub-pat', variable: 'DOCKERHUB_TOKEN')]) {
                        sh "echo $DOCKERHUB_TOKEN | docker login -u $DOCKERHUB_USERNAME --password-stdin"
                        sh "docker push ${IMAGE_NAME}:${BUILD_NUMBER}"
                        sh "docker tag ${IMAGE_NAME}:${BUILD_NUMBER} ${IMAGE_NAME}:latest"
                        sh "docker push ${IMAGE_NAME}:latest"
                    }
                }
            }
        }
    }

    post {
        success { echo 'Pipeline succeeded!' }
        failure { echo 'Pipeline failed!' }
    }
}
