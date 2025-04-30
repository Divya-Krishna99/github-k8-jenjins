pipeline {
  agent any

  environment {
    IMAGE = "divyakrishna123/hello-app:v1"
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Build Image') {
      steps {
        sh "docker build -t $IMAGE ."
      }
    }

    stage('Push to DockerHub') {
      steps {
        withCredentials([usernamePassword(
          credentialsId: 'dockerhub-creds',
          usernameVariable: 'DOCKER_USER',
          passwordVariable: 'DOCKER_PASS'
        )]) {
          sh '''
            echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
            docker push $IMAGE
          '''
        }
      }
    }

    stage('Deploy to Kubernetes') {
      steps {
        // adjust path if needed, e.g. k8s/deployment.yml
        sh 'kubectl apply -f deployment.yml'
        sh 'kubectl apply -f service.yml'
      }
    }
  }
}
