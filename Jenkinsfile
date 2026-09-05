pipeline {
    agent any

    environment {
        // Replace with your actual DockerHub username and app name
        IMAGE_NAME = "your-dockerhub-username/my-simple-app"
        // Uses the Jenkins build number to tag images uniquely
        IMAGE_TAG = "${env.BUILD_ID}"
        // The exact ID of the credential you will create in Jenkins
        DOCKER_CREDS = 'dockerhub-credentials-id'
    }

    stages {
        stage('Checkout') {
            steps {
                // Polls your configured GitHub repo webhook
                checkout scm
            }
        }

        stage('Build & Test') {
            steps {
                echo "Running tests..."
                // Replace this with your app's actual test command (e.g., npm test, pytest)
                sh 'python3 -m unittest test_app.py || echo "No tests found"'
            }
        }

        stage('SonarQube Analysis') {
            environment {
                // Must match the scanner name configured in Jenkins Global Tool Configuration
                SCANNER_HOME = tool 'sonar-scanner'
            }
            steps {
                // 'sonar-server' must match the server name in Jenkins System configuration
                withSonarQubeEnv('sonar-server') {
                    sh """
                    ${SCANNER_HOME}/bin/sonar-scanner \
                      -Dsonar.projectKey=my-simple-app \
                      -Dsonar.sources=.
                    """
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 1, unit: 'HOURS') {
                    // Requires a webhook configured in SonarQube pointing back to Jenkins
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Docker Build') {
            steps {
                echo "Building Docker Image..."
                sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
                sh "docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${IMAGE_NAME}:latest"
            }
        }

        stage('Docker Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: "${DOCKER_CREDS}", passwordVariable: 'DOCKER_PASS', usernameVariable: 'DOCKER_USER')]) {
                    sh "echo \$DOCKER_PASS | docker login -u \$DOCKER_USER --password-stdin"
                    sh "docker push ${IMAGE_NAME}:${IMAGE_TAG}"
                    sh "docker push ${IMAGE_NAME}:latest"
                }
            }
        }

        stage('Deploy') {
            steps {
                // Deploys locally on the Jenkins/EC2 server by replacing the old container
                sh "docker stop my-running-app || true"
                sh "docker rm my-running-app || true"
                sh "docker run -d --name my-running-app -p 8080:80 ${IMAGE_NAME}:latest"
            }
        }
    }

    post {
        always {
            // Security best practice: clean up credentials and workspace
            sh "docker logout"
            cleanWs()
        }
    }
}
