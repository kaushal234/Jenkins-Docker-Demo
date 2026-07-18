pipeline {

    agent any

    environment {
        IMAGE_NAME = 'jenkins-docker-image'
        CONTAINER_NAME = 'jenkins-docker-container'
        HOST_PORT = '8081'
        IMAGE_TAG = '1.0.0'
        EC2_URL = 'http://3.110.85.83'
    }

    stages {

        stage('Checkout Code') {
            steps {
                echo 'Downloading the code from repository'
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image'
                sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
            }
        }

        stage('Remove Old Container') {
            steps {
                echo 'Removing old container'

                sh '''
                docker stop ${CONTAINER_NAME} || true
                docker rm ${CONTAINER_NAME} || true
                '''
            }
        }

        stage('Run Docker Container') {
            steps {
                echo 'Starting Docker container'

                sh """
                docker run -d \
                --name ${CONTAINER_NAME} \
                -p ${HOST_PORT}:80 \
                ${IMAGE_NAME}:${IMAGE_TAG}
                """
            }
        }

        stage('Test Application') {
            steps {
                echo 'Testing application through EC2 public URL'

                sh "curl --fail ${EC2_URL}:${HOST_PORT}"
            }
        }
    }

    post {

        success {
            echo 'Pipeline completed successfully.'
        }

        failure {
            echo 'Pipeline failed.'
        }
    }
}
