pipeline {
    agent {
        kubernetes {
            inheritFrom 'kaniko-pod'
            defaultContainer 'kaniko'
            yaml """
apiVersion: v1
kind: Pod
spec:
  containers:
    - name: kaniko
      image: gcr.io/kaniko-project/executor:latest
      args: ["--dockerfile=Dockerfile", "--context=/workspace"]
      volumeMounts:
        - name: kaniko-secret
          mountPath: /kaniko/.docker
  volumes:
    - name: kaniko-secret
      secret:
        secretName: dockerhub-config
"""
        }
    }

    triggers {
        githubPush()
    }

    environment {
        DOCKER_REG  = 'pinkmelon'
        IMAGE_REPO  = "${env.DOCKER_REG}/hw-spring-product"
        IMAGE_TAG   = "${env.BUILD_NUMBER}"
        CD_BRANCH   = 'master'
    }

    stages {
        stage('Checkout Source') {
            steps {
                git branch: "${CD_BRANCH}", url: 'https://github.com/Thavornn/17_Yin_Chheng-Thavorn_SR_SPRING_HOMEWORK001', credentialsId: 'github-token'
            }
        }

        stage('Build & Push Docker Image') {
            steps {
                container('kaniko') {
                    sh """
                    /kaniko/executor \
                        --dockerfile=Dockerfile \
                        --context=\$PWD \
                        --destination=${IMAGE_REPO}:${IMAGE_TAG} \
                        --destination=${IMAGE_REPO}:latest
                    """
                }
            }
        }
    }

    post {
        success {
            echo '✅ Build & Push completed successfully!'
        }
        failure {
            echo '❌ Pipeline failed.'
        }
    }
}