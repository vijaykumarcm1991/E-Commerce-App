pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-creds') // Replace with your actual credentials ID if different
        BACKEND_IMAGE = "vijaykumarcm/ecom-backend"
        FRONTEND_IMAGE = "vijaykumarcm/ecom-frontend"
    }

    stages {
        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }

        stage('Checkout Code') {
            steps {
                git url: 'https://github.com/vijaykumarcm1991/E-Commerce-App.git', branch: 'main'
            }
        }

        stage('Build Backend Docker Image') {
            steps {
                dir('backend') {
                    sh """
                    docker build -t $BACKEND_IMAGE:${BUILD_NUMBER} .
                    """
                }
            }
        }

        stage('Build Frontend Docker Image') {
            steps {
                dir('frontend') {
                    sh """
                    docker build -t $FRONTEND_IMAGE:${BUILD_NUMBER} .
                    """
                }
            }
        }

        stage('Push Backend Image to DockerHub') {
            steps {
                withDockerRegistry([credentialsId: 'dockerhub-creds', url: 'https://index.docker.io/v1/']) {
                    sh "docker push $BACKEND_IMAGE:${BUILD_NUMBER}"
                }
            }
        }

        stage('Push Frontend Image to DockerHub') {
            steps {
                withDockerRegistry([credentialsId: 'dockerhub-creds', url: 'https://index.docker.io/v1/']) {
                    sh "docker push $FRONTEND_IMAGE:${BUILD_NUMBER}"
                }
            }
        }

        stage('Helm Deploy to Kubernetes') {
            steps {
                dir('charts/ecommerce') {
                    sh """
                    helm upgrade --install ecommerce . \
                      --set backend.image.repository=$BACKEND_IMAGE \
                      --set backend.image.tag=${BUILD_NUMBER} \
                      --set frontend.image.repository=$FRONTEND_IMAGE \
                      --set frontend.image.tag=${BUILD_NUMBER}
                    """
                }
            }
        }
    }

    post {
        success {
            cleanWs()
        }
        failure {
            echo 'Build failed. Workspace not cleaned for debugging.'
        }
    }
}
