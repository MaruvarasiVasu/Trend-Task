pipeline {
    agent any

    environment {
        IMAGE_NAME = "maruvarasivasu/trend-app"  // DockerHub repository
        AWS_REGION = "ap-south-1"
        CLUSTER_NAME = "trend-cluster"
        NAMESPACE = "trend"
    }

    stages {
        stage('Checkout Code') {
            steps {
                // Pull code from GitHub
                git branch: 'main', url: 'https://github.com/MaruvarasiVasu/Trend-Task.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                // Build Docker image with Jenkins BUILD_NUMBER tag
                sh "docker build -t $IMAGE_NAME:$BUILD_NUMBER ."
            }
        }

        stage('Push Docker Image') {
            steps {
                // Login to DockerHub and push image using Jenkins credentials (ID = 'admin')
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

        stage('Deploy to Kubernetes (EKS)') {
            steps {
                // Deploy to EKS using kubeconfig (upload as Jenkins secret file with ID 'kubeconfig-cred')
                withCredentials([file(credentialsId: 'kubeconfig-cred', variable: 'KUBECONFIG')]) {
                    sh '''
                        # Ensure namespace exists
                        kubectl create namespace $NAMESPACE --dry-run=client -o yaml | kubectl apply -f -
                        
                        # Deploy Docker image
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
