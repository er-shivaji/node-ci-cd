pipeline {
  agent any

  environment {
    IMAGE_NAME = "my-app"
    TAG = "latest"
  }

  stages {
    stage('Checkout') {
      steps {
        git url: 'https://github.com/er-shivaji/node-ci-cd.git', branch: 'main'
      }
    }

    stage('Build Docker Image') {
      steps {
        script {
          sh 'eval $(minikube -p minikube docker-env)'  // enable minikube Docker
          sh "docker build -t ${IMAGE_NAME}:${TAG} ."
        }
      }
    }

    stage('Deploy to Minikube') {
      steps {
        script {
          sh "kubectl apply -f deployment.yaml"
          sh "kubectl apply -f service.yaml"
        }
      }
    }
  }
}
