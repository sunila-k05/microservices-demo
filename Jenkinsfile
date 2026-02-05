pipeline {
    agent any

    options {
        disableConcurrentBuilds()
        timestamps()
    }

    environment {
        REGISTRY   = "ghcr.io/sunila-k05"
        IMAGE_NAME = "frontend"
        IMAGE_TAG  = "latest"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                docker build -t $REGISTRY/$IMAGE_NAME:$IMAGE_TAG src/frontend
                '''
            }
        }

    stage('Login to GHCR') {
  steps {
    withCredentials([usernamePassword(
      credentialsId: 'GHCR_TOKEN',
      usernameVariable: 'GITHUB_USER',
      passwordVariable: 'GITHUB_TOKEN'
    )]) {
      sh '''
        echo "$GITHUB_TOKEN" | docker login ghcr.io \
          -u "$GITHUB_USER" --password-stdin
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
        export KUBECONFIG=/var/lib/jenkins/.kube/config
        kubectl rollout restart deployment frontend
        '''
    }
}

}
    post {
        success {
            echo "✅ Pipeline completed successfully"
        }
        failure {
            echo "❌ Pipeline failed"
        }
    }
}
