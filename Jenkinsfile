pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/MaruvarasiVasu/Trend-Task.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t maruvarasivasu/trend-app:latest .'
            }
        }

        stage('Push Docker Image') {
            steps {
                sh 'docker login -u maruvarasivasu -p Sanyash@2021'
                sh 'docker push maruvarasivasu/trend-app:latest'
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh 'kubectl apply -f deployment.yaml'
                sh 'kubectl apply -f service.yaml'
            }
        }
    }
}
