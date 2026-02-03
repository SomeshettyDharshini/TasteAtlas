pipeline {
    agent any

    environment {
        DOCKERHUB_USERNAME = "someshettydharshini"
        IMAGE_NAME = "tasteatlas"
        IMAGE_TAG = "latest"
    }

    stages {

        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
            }
        }

        stage('Tag Docker Image') {
            steps {
                sh "docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${DOCKERHUB_USERNAME}/${IMAGE_NAME}:${IMAGE_TAG}"
            }
        }

        stage('Push Docker Image') {
            steps {
                sh "docker push ${DOCKERHUB_USERNAME}/${IMAGE_NAME}:${IMAGE_TAG}"
            }
        }
    }
}
