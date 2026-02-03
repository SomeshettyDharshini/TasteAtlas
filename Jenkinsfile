pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-creds')  // Docker Hub credentials
        IMAGE_NAME = "someshettydharshini/tasteatlas"           // Docker Hub image name
        KUBE_CONFIG = "/home/jenkins/.kube/config"             // Path to kubeconfig on Jenkins server
    }

    stages {
        stage('Checkout Code') {
            steps {
                // Pull code from GitHub
                git branch: 'main', url: 'https://github.com/someshettydharshini/tasteatlas.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Build Docker image
                    sh "docker build -t ${IMAGE_NAME}:latest ."
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    // Login to Docker Hub and push image
                    sh """
                    echo ${DOCKERHUB_CREDENTIALS_PSW} | docker login -u ${DOCKERHUB_CREDENTIALS_USR} --password-stdin
                    docker push ${IMAGE_NAME}:latest
                    """
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    // Use kubeconfig and update deployment image
                    sh """
                    export KUBECONFIG=${KUBE_CONFIG}

                    # Update Kubernetes deployment with new image
                    kubectl set image deployment/tasteatlas-deployment tasteatlas=${IMAGE_NAME}:latest --record

                    # Check rollout status
                    kubectl rollout status deployment/tasteatlas-deployment
                    """
                }
            }
        }
    }

    post {
        success {
            echo "Pipeline completed successfully!"
        }
        failure {
            echo "Pipeline failed. Check the logs for errors."
        }
    }
}
