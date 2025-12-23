pipeline {
    agent any

    options {
        disableConcurrentBuilds()
    }

    environment {
        REGISTRY = "ghcr.io/sunila-k05"
        IMAGE = "frontend"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Image') {
            steps {
                sh '''
                docker build -t $REGISTRY/$IMAGE:latest src/frontend
                '''
            }
        }

        stage('Push Image') {
            steps {
                sh '''
                echo "$GHCR_TOKEN" | docker login ghcr.io -u sunila-k05 --password-stdin
                docker push $REGISTRY/$IMAGE:latest
                '''
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                kubectl rollout restart deployment frontend
                '''
            }
        }
    }
}
