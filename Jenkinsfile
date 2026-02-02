pipeline {
    agent any

    environment {
        IMAGE_NAME = "tasteatlas"
        IMAGE_TAG  = "latest"
        DOCKER_REPO = "<YOUR_DOCKER_REPO>" // e.g., dockerhubusername/tasteatlas
        KUBE_NAMESPACE = "default"
        DEPLOYMENT_NAME = "tasteatlas-deployment"
    }

    stages {
        stage('Build Docker Image') {
            steps {
                echo "Building Docker image..."
                sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
            }
        }

        stage('Deploy Container Locally') {
            steps {
                echo "Deploying container locally..."
                sh '''
                docker stop ${IMAGE_NAME} || true
                docker rm ${IMAGE_NAME} || true
                docker run -d -p 8080:80 --name ${IMAGE_NAME} ${IMAGE_NAME}:${IMAGE_TAG}
                '''
            }
        }

        stage('Push Docker Image') {
            steps {
                echo "Pushing image to Docker registry..."
                sh """
                docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${DOCKER_REPO}:${IMAGE_TAG}
                docker push ${DOCKER_REPO}:${IMAGE_TAG}
                """
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                echo "Deploying to Kubernetes..."
                sh """
                kubectl apply -n ${KUBE_NAMESPACE} -f - <<EOF
                apiVersion: apps/v1
                kind: Deployment
                metadata:
                  name: ${DEPLOYMENT_NAME}
                spec:
                  replicas: 2
                  selector:
                    matchLabels:
                      app: ${IMAGE_NAME}
                  template:
                    metadata:
                      labels:
                        app: ${IMAGE_NAME}
                    spec:
                      containers:
                      - name: ${IMAGE_NAME}
                        image: ${DOCKER_REPO}:${IMAGE_TAG}
                        ports:
                        - containerPort: 80
                ---
                apiVersion: v1
                kind: Service
                metadata:
                  name: ${IMAGE_NAME}-service
                spec:
                  type: NodePort
                  selector:
                    app: ${IMAGE_NAME}
                  ports:
                    - protocol: TCP
                      port: 80
                      targetPort: 80
                EOF
                """
            }
        }
    }
}
