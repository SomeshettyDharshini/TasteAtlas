pipeline {
    agent any

    environment {
        DOCKERHUB_USERNAME = "someshettydharshini"
        IMAGE_NAME = "tasteatlas"
        IMAGE_TAG = "latest"
        CONTAINER_NAME = "tasteatlas-container"
    }

    stages {

        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "Building Docker Image..."
                sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
            }
        }

        stage('Login to DockerHub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-credentials',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {

                    sh """
                    echo \$DOCKER_PASS | docker login -u \$DOCKER_USER --password-stdin
                    """
                }
            }
        }

        stage('Push Image to DockerHub') {
            steps {
                echo "Pushing image to DockerHub..."

                sh """
                docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${DOCKERHUB_USERNAME}/${IMAGE_NAME}:${IMAGE_TAG}
                docker push ${DOCKERHUB_USERNAME}/${IMAGE_NAME}:${IMAGE_TAG}
                """
            }
        }

        stage('Deploy Container Locally') {
            steps {
                echo "Running Docker Container..."

                sh """
                docker stop ${CONTAINER_NAME} || true
                docker rm ${CONTAINER_NAME} || true
                docker run -d -p 8080:80 --name ${CONTAINER_NAME} ${DOCKERHUB_USERNAME}/${IMAGE_NAME}:${IMAGE_TAG}
                """
            }
        }
    }

    post {
        success {
            echo "Application deployed successfully on port 8080 🎉"
        }
        failure {
            echo "Pipeline failed ❌ Check logs"
        }
    }
}
