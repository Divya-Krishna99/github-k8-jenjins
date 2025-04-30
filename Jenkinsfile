pipeline {
    agent any

    environment {
        IMAGE = "divyakrishna123/hello-app:v1"
    }

    stages {
        stage('Checkout') {
            steps {
                // Use the SCM configuration from the Jenkins job
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
                sh 'kubectl apply -f k8s/deployment.yml'
                sh 'kubectl apply -f k8s/service.yml'
            }
        }
    }
}