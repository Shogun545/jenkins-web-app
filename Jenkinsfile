pipeline {
    agent any

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
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub',
                    usernameVariable: 'USER',
                    passwordVariable: 'PASS'
                )]) {
                    sh '''
                    echo $PASS | docker login -u $USER --password-stdin
                    docker push phuwit09/my-nginx:${IMAGE_TAG}
                    docker push phuwit09/my-nginx:latest
                    '''
                }
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