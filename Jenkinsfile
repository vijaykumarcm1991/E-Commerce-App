pipeline {
  agent any
  environment {
    FRONTEND_IMAGE = "vijaykumarcm/ecom-frontend"
    BACKEND_IMAGE  = "vijaykumarcm/ecom-backend"
    TAG = "${BUILD_NUMBER}"
  }
  stages {
    stage('Checkout') {
      steps {
        git url: 'https://github.com/vijaykumarcm1991/E-Commerce-App.git', branch: 'main'
      }
    }

    stage('Build & Push Docker Images') {
      steps {
        script {
          sh "docker build -t $FRONTEND_IMAGE:$TAG ./frontend"
          sh "docker build -t $BACKEND_IMAGE:$TAG ./backend"
          sh "docker push $FRONTEND_IMAGE:$TAG"
          sh "docker push $BACKEND_IMAGE:$TAG"
        }
      }
    }

    stage('Deploy with Helm') {
      steps {
        sh "helm upgrade --install ecommerce ./charts/ecommerce \
            --set frontend.image.tag=$TAG \
            --set backend.image.tag=$TAG"
      }
    }
  }
  
  post {
    always {
      echo "Cleaning up workspace..."
      cleanWs()
    }
    success {
      echo "Deployment successful!"
    }
    failure {
      echo "Deployment failed!"
    }
  }
}