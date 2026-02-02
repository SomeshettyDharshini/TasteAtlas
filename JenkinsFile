pipeline {
    agent any

    stages {
        stage('Build Image') {
            steps {
                sh 'docker build -t static-web .'
            }
        }

        stage('Deploy Container') {
            steps {
                sh '''
                docker stop static-web || true
                docker rm static-web || true
                docker run -d -p 8080:80 --name static-web static-web
                '''
            }
        }
    }
}
