
pipeline {
    agent any
    stages {
        stage('Test') {
            steps {
                echo "Hello Jenkins! Pipeline is working."
            }
        }
    }
    post {
        success {
            echo "✅ Pipeline succeeded!"
        }
        failure {
            echo "❌ Pipeline failed!"
        }
    }
}
