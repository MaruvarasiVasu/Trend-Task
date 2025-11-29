pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps { git 'https://github.com/MaruvarasiVasu/Trend-Task.git' }
        }
        stage('Build Docker') {
            steps { sh 'docker build -t maruvarasivasu/trend-task:latest .' }
        }
        stage('Push Docker') {
            steps {
                sh 'docker login -u maruvarasivasu -p Sanyash@2021'
                sh 'docker push maruvarasivasu/trend-task:latest'
            }
        }
        stage('Deploy') {
            steps {
                sh 'kubectl apply -f deployment.yaml'
                sh 'kubectl apply -f service.yaml'
            }
        }
    }
}
