pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('admin')    // Jenkins DockerHub credential
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
                sh "docker build -t $IMAGE_NAME:$BUILD_NUMBER ."
            }
        }

        stage('Push Docker Image') {
            steps {
                sh '''
                    echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin
                    docker push $IMAGE_NAME:$BUILD_NUMBER
                    docker tag $IMAGE_NAME:$BUILD_NUMBER $IMAGE_NAME:latest
                    docker push $IMAGE_NAME:latest
                '''
            }
        }

        stage('Deploy to EKS') {
            steps {
                // This block will use kubeconfig once you upload it as Jenkins secret
                withCredentials([file(credentialsId: 'kubeconfig-cred', variable: 'KUBECONFIG')]) {
                    sh '''
                        # Ensure namespace exists
                        kubectl create namespace $NAMESPACE --dry-run=client -o yaml | kubectl apply -f -
                        
                        # Deploy new Docker image
                        kubectl set image deployment/trend-app trend-app=$IMAGE_NAME:$BUILD_NUMBER -n $NAMESPACE
                        kubectl rollout status deployment/trend-app -n $NAMESPACE
                    '''
                }
            }
        }
    }

    post {
        success {
            echo '✅ Deployment completed successfully!'
        }
        failure {
            echo '❌ Deployment failed. Check Jenkins logs.'
        }
    }
}
