pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "flask-portfolio"
        CONTAINER_NAME = "flask-portfolio-container"
        DOCKERHUB_USER = "sombratasatpathy"      // ✅ Docker Hub usernames must be lowercase (very important)
        DOCKERHUB_PASS = credentials('dockerhub-credentials')
        IMAGE_TAG = "latest"
        DEPLOYMENT_FILE = "deployment.yaml"
        SERVICE_FILE = "service.yaml"
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
                    // ✅ Update deployment image dynamically before applying
                    sh '''
                    kubectl delete -f ${DEPLOYMENT_FILE} --ignore-not-found
                    kubectl delete -f ${SERVICE_FILE} --ignore-not-found

                    # Replace image name in deployment
                    sed -i "s|image:.*|image: ${DOCKERHUB_USER}/${DOCKER_IMAGE}:${IMAGE_TAG}|" ${DEPLOYMENT_FILE}

                    kubectl apply -f ${DEPLOYMENT_FILE}
                    kubectl apply -f ${SERVICE_FILE}
                    kubectl rollout status deployment/flask-deployment
                    '''
                }
            }
        }
    }

    post {
        success {
            echo '✅ Kubernetes Deployment successful!'
            script {
                sh 'kubectl get svc flask-service'
            }
        }
        failure {
            echo '❌ Build or Deployment failed!'
        }
    }
}
