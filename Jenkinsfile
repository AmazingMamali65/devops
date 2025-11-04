pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "flask-portfolio"
        CONTAINER_NAME = "flask-portfolio-container"
        DOCKERHUB_USER = "Sombrata Satpathy"
        DOCKERHUB_PASS = credentials('dockerhub-credentials')
        IMAGE_TAG = "latest"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/AmazingMamali65/devops.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh 'docker build -t ${DOCKERHUB_USER}/${DOCKER_IMAGE}:${IMAGE_TAG} .'
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    sh '''
                    echo $DOCKERHUB_PASS | docker login -u $DOCKERHUB_USER --password-stdin
                    docker push ${DOCKERHUB_USER}/${DOCKER_IMAGE}:${IMAGE_TAG}
                    '''
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    // Assuming deployment.yaml and service.yaml exist in repo
                    sh '''
                    kubectl apply -f deployment.yaml
                    kubectl apply -f service.yaml
                    '''
                }
            }
        }
    }

    post {
        success {
            echo '✅ Kubernetes Deployment successful!'
        }
        failure {
            echo '❌ Build or Deployment failed!'
        }
    }
}
