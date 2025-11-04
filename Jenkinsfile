pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "flask-portfolio"
        CONTAINER_NAME = "flask-portfolio-container"
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
                    sh 'docker build -t ${DOCKER_IMAGE} .'
                }
            }
        }

        stage('Run Container') {
            steps {
                script {
                    // Stop and remove old container if running
                    sh '''
                    docker ps -q --filter "name=${CONTAINER_NAME}" | grep -q . && docker stop ${CONTAINER_NAME} && docker rm ${CONTAINER_NAME} || true
                    docker run -d -p 5000:5000 --name ${CONTAINER_NAME} ${DOCKER_IMAGE}
                    '''
                }
            }
        }
    }

    post {
        success {
            echo '✅ Deployment successful!'
        }
        failure {
            echo '❌ Build failed!'
        }
    }
}
