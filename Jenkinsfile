pipeline {
    agent any
    
    environment {
        DOCKER_REGISTRY = "localhost:5000"
        APP_NAME = "html-app"
        K8S_NAMESPACE = "myapp"
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Build Docker Image') {
            steps {
                script {
                    sh """
                        docker build -t ${DOCKER_REGISTRY}/${APP_NAME}:${BUILD_NUMBER} .
                        docker push ${DOCKER_REGISTRY}/${APP_NAME}:${BUILD_NUMBER}
                        docker tag ${DOCKER_REGISTRY}/${APP_NAME}:${BUILD_NUMBER} ${DOCKER_REGISTRY}/${APP_NAME}:latest
                        docker push ${DOCKER_REGISTRY}/${APP_NAME}:latest
                    """
                }
            }
        }
        
        stage('Deploy to Kubernetes') {
            steps {
                script {
                    sh """
                        kubectl -n ${K8S_NAMESPACE} apply -f k8s/deployment.yaml
                        kubectl -n ${K8S_NAMESPACE} apply -f k8s/service.yaml
                        kubectl -n ${K8S_NAMESPACE} set image deployment/${APP_NAME} ${APP_NAME}=${DOCKER_REGISTRY}/${APP_NAME}:${BUILD_NUMBER}
                    """
                }
            }
        }
    }
}