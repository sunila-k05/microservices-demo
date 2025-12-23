pipeline {
    agent any

    options {
        disableConcurrentBuilds()
        timestamps()
    }

    environment {
        REGISTRY        = "ghcr.io/sunila-k05"
        IMAGE_NAME      = "frontend"
        IMAGE_TAG       = "latest"
        DOCKER_BUILDKIT = "1"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image (BuildKit)') {
            steps {
                sh '''
                docker buildx create --use --name jenkins-builder || true

                docker buildx build \
                  --platform linux/amd64 \
                  -t $REGISTRY/$IMAGE_NAME:$IMAGE_TAG \
                  src/frontend
                '''
            }
        }

        stage('Login to GHCR') {
            steps {
                withCredentials([string(credentialsId: 'GHCR_TOKEN', variable: 'TOKEN')]) {
                    sh '''
                    echo "$TOKEN" | docker login ghcr.io \
                      -u sunila-k05 \
                      --password-stdin
                    '''
                }
            }
        }

        stage('Push Image') {
            steps {
                sh '''
                docker push $REGISTRY/$IMAGE_NAME:$IMAGE_TAG
                '''
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                kubectl rollout restart deployment frontend
                kubectl rollout status deployment frontend
                '''
            }
        }
    }

    post {
        always {
            sh 'docker buildx rm jenkins-builder || true'
        }
        success {
            echo "✅ Pipeline completed successfully"
        }
        failure {
            echo "❌ Pipeline failed"
        }
    }
}
