pipeline {
    agent any

    environment {
        IMAGE_NAME = "maruvarasivasu/trend-app"
        AWS_REGION = "ap-south-1"
        CLUSTER_NAME = "trend-cluster"
        NAMESPACE = "trend"
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/MaruvarasiVasu/Trend-Task.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME:$BUILD_NUMBER .'
            }
        }

        stage('Push to DockerHub') {
            steps {
                // Inject DockerHub credentials safely
                withCredentials([usernamePassword(credentialsId: 'admin', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh '''
                        echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                        docker push $IMAGE_NAME:$BUILD_NUMBER
                        docker tag $IMAGE_NAME:$BUILD_NUMBER $IMAGE_NAME:latest
                        docker push $IMAGE_NAME:latest
                    '''
                }
            }
        }

        stage('Deploy to Kubernetes (EKS)'
