pipeline {
    agent any

    environment {
        IMAGE_NAME = "maruvarasivasu/trend-task"
        IMAGE_TAG = "${env.BUILD_NUMBER}"
        DOCKER_CREDENTIALS_ID = "dockerhub-creds"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "🛠 Building Docker image..."
                script {
                    dockerImage = docker.build("${IMAGE_NAME}:${IMAGE_TAG}")
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                echo "📦 Pushing Docker image to Docker Hub..."
                script {
                    docker.withRegistry('', DOCKER_CREDENTIALS_ID) {
                        dockerImage.push("${IMAGE_TAG}")
                        dockerImage.push("latest")
                    }
                }
            }
        }
    }

    post {
        success {
            echo "✅ Docker build & push succeeded!"
        }
        failure {
            echo "❌ Pipeline failed. Check Jenkins logs."
        }
        always {
            echo "📌 Pipeline finished at ${new Date()}"
        }
    }
}
