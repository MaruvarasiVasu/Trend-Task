pipeline {
    agent any   // Run on any available Jenkins node

    environment {
        // Add environment variables if needed
        DEPLOY_ENV = "production"
    }

    stages {
        stage('Checkout') {
            steps {
                echo "🔄 Checking out source code..."
                checkout scm
            }
        }

        stage('Build') {
            steps {
                echo "🛠 Building the application..."
                // Replace with your actual build command, e.g.:
                // sh './gradlew build'
                sh 'echo "Simulated build step"'
            }
        }

        stage('Test') {
            steps {
                echo "✅ Running tests..."
                // Replace with your actual test command, e.g.:
                // sh './gradlew test'
                sh 'echo "Simulated test step"'
            }
        }

        stage('Deploy') {
            steps {
                echo "🚀 Deploying to ${env.DEPLOY_ENV}..."
                // Replace with your actual deploy command, e.g.:
                // sh './deploy.sh'
                sh 'echo "Simulated deploy step"'
            }
        }
    }

    post {
        success {
            echo "🎉 Deployment succeeded!"
        }
        failure {
            echo "❌ Deployment failed. Check Jenkins logs for details."
        }
        always {
            echo "📌 Pipeline finished at ${new Date()}"
        }
    }
}
