pipeline {
    agent any

    environment {
        IMAGE_NAME = "maruvarasivasu/trend-task"
        IMAGE_TAG = "${env.BUILD_NUMBER}"
        DOCKER_CREDENTIALS_ID = "docker-hub-credentials"  // use Jenkins‑stored credentials
        KUBE_CONFIG_CREDENTIALS_ID = "kubeconfig‑creds"   // if using kubeconfig for k8s
        KUBE_NAMESPACE = "default"                        // or your namespace
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("${IMAGE_NAME}:${IMAGE_TAG}")
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('', DOCKER_CREDENTIALS_ID) {
                        dockerImage.push("${IMAGE_TAG}")
                        dockerImage.push("latest")
                    }
                }
            }
        }

        stage('Deploy to Cluster') {
            steps {
                withCredentials([file(credentialsId: KUBE_CONFIG_CREDENTIALS_ID, variable: 'KUBECONFIG')]) {
                    sh """
                      kubectl --kubeconfig=$KUBECONFIG -n ${KUBE_NAMESPACE} apply -f deployment.yaml
                      kubectl --kubeconfig=$KUBECONFIG -n ${KUBE_NAMESPACE} rollout status deployment/<your-deployment-name>
                    """
                }
            }
        }
    }

    post {
        success {
            echo "✅ Build, push and deploy succeeded"
        }
        failure {
            echo "❌ Pipeline failed – check logs"
        }
        always {
            echo "📌 Pipeline finished"
        }
    }
}
