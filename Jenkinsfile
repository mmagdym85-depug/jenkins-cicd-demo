pipeline {
    agent any

    environment {
        IMAGE_NAME = "jenkins-cicd-demo"
        IMAGE_TAG = "${BUILD_NUMBER}"
        CONTAINER_NAME = "jenkins-cicd-demo"
        APP_PORT = "8081"
    }

    stages {

        stage('Validate') {
            steps {
                sh '''
                    set -e

                    echo "Checking files..."

                    test -f app.txt
                    test -f Dockerfile

                    echo "Validation successful"
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                    set -e

                    docker build \
                    -t ${IMAGE_NAME}:${IMAGE_TAG} .
                '''
            }
        }

        stage('Test Container') {
            steps {
                sh '''
                    set -e

                    docker rm -f test-container 2>/dev/null || true

                    docker run -d \
                    --name test-container \
                    -p 18081:80 \
                    ${IMAGE_NAME}:${IMAGE_TAG}

                    sleep 3

                    curl -f http://localhost:18081

                    docker rm -f test-container
                '''
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                    set -e

                    docker rm -f ${CONTAINER_NAME} 2>/dev/null || true

                    docker run -d \
                    --name ${CONTAINER_NAME} \
                    -p ${APP_PORT}:80 \
                    ${IMAGE_NAME}:${IMAGE_TAG}
                '''
            }
        }

        stage('Health Check') {
            steps {
                sh '''
                    set -e

                    sleep 3

                    curl -f http://localhost:${APP_PORT}

                    echo ""
                    echo "Application is healthy!"
                '''
            }
        }
    }

    post {
        success {
            echo "CI/CD Pipeline completed successfully."
        }

        failure {
            echo "CI/CD Pipeline failed."
        }
    }
}
