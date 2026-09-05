pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                // Automatically pulls the GitHub repository configured in your Jenkins job
                checkout scm
            }
        }

        stage('Build') {
            steps {
                echo 'Building repository contents...'
                // Verify files were pulled successfully
                sh 'ls -la' 
            }
        }

        stage('Deploy') {
            steps {
                echo 'Executing deployment steps...'
                // Replace with your actual deployment script
                // sh './deploy.sh' 
            }
        }
    }
}
