pipeline {
    agent any

    options {
        timeout(time: 1, unit: 'HOURS')
        ansiColor('xterm')
    }

    stages {
        stage('Initialize') {
            steps {
                echo 'Checking workspace and runner info...'
                sh '''
                    echo "Current directory: $(pwd)"
                    echo "User: $(whoami)"
                    echo "Hostname: $(hostname)"
                '''
            }
        }

        stage('Execute Tasks') {
            steps {
                echo 'Running primary script steps...'
                sh '''
                    echo "Starting execution..."
                    # Put your custom shell commands or script calls here:
                    # python3 script.py
                    # ./run.sh
                '''
            }
        }

        stage('Reporting') {
            steps {
                echo 'Generating logs and wrapping up...'
            }
        }
    }

    post {
        always {
            cleanWs()
        }
        success {
            echo 'Pipeline finished with status: SUCCESS'
        }
        failure {
            echo 'Pipeline finished with status: FAILURE'
        }
    }
}
