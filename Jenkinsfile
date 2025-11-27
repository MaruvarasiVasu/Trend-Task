pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('admin')
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
                sh '''
                    echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin
                    docker push $IMAGE_NAME:$BUILD_NUMBER
                    docker tag $IMAGE_NAME:$BUILD_NUMBER $IMAGE_NAME:latest
                    docker push $IMAGE_NAME:latest
                '''
            }
        }

        stage('Deploy to Kubernetes (EKS)') {
            steps {
                sh '''
                    aws eks update-kubeconfig --region $AWS_REGION --name $CLUSTER_NAME
                    kubectl create namespace $NAMESPACE || true

                    # Deploy with specific build tag
                    kubectl set image deployment/trend-app trend-app=$IMAGE_NAME:$BUILD_NUMBER -n $NAMESPACE
                    kubectl rollout status deployment/trend-app -n $NAMESPACE
                '''
            }
        }
    }

    post {
        success {
            echo '✅ Deployment completed successfully! Your app should be accessible via the LoadBalancer.'
        }
        failure {
            echo '❌ Deployment failed. Please check Jenkins logs for details.'
        }
    }
}
