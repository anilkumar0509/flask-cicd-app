pipeline {
    agent any

    environment {
        IMAGE = "venkataanil050906/flask-cicd-app"
        TAG   = "${BUILD_NUMBER}"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE:$TAG .'
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds',
                                 usernameVariable: 'DH_USER',
                                 passwordVariable: 'DH_PASS')]) {
                    sh 'echo $DH_PASS | docker login -u $DH_USER --password-stdin'
                    sh 'docker push $IMAGE:$TAG'
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh 'sed -i "s|IMAGE_PLACEHOLDER|$IMAGE:$TAG|" deployment.yaml'
                sh 'kubectl apply -f deployment.yaml'
                sh 'kubectl rollout status deployment/flask-app --timeout=180s'
            }
        }
    }
}
