pipeline {
    agent { label 'k8s-agent-1' }

    environment {
        DOCKER_USER = 'phuwit09'
        APP_NAME = 'my-nginx'
        IMAGE_TAG = "${BUILD_NUMBER}"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh "docker build -t ${DOCKER_USER}/${APP_NAME}:${IMAGE_TAG} ."
                sh "docker tag ${DOCKER_USER}/${APP_NAME}:${IMAGE_TAG} ${DOCKER_USER}/${APP_NAME}:latest"
            }
        }

        stage('Push') {
            steps {
                sh "docker push ${DOCKER_USER}/${APP_NAME}:${IMAGE_TAG}"
                sh "docker push ${DOCKER_USER}/${APP_NAME}:latest"
            }
        }

        stage('Deploy') {
            steps {
                sh "kubectl apply -f k8s/"
                sh "kubectl set image deployment/nginx-deployment nginx-container=${DOCKER_USER}/${APP_NAME}:${IMAGE_TAG}"
            }
        }

        stage('Verify') {
            steps {
                sh "kubectl rollout status deployment/nginx-deployment"
                sh "kubectl get pods"
            }
        }
    }
}