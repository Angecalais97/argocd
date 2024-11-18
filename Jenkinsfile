pipeline {
    agent any
    environment {
        DOCKER_IMAGE = 'your-dockerhub-username/argocd-test-app'
        ARGOCD_SERVER = 'https://<argocd-server>'
        ARGOCD_AUTH_TOKEN = credentials('argocd-token') // Store this in Jenkins credentials
    }
    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }
        stage('Build Docker Image') {
            steps {
                sh '''
                docker build -t $DOCKER_IMAGE:${BUILD_NUMBER} .
                '''
            }
        }
        stage('Push Docker Image') {
            steps {
                sh '''
                docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD
                docker push $DOCKER_IMAGE:${BUILD_NUMBER}
                docker tag $DOCKER_IMAGE:${BUILD_NUMBER} $DOCKER_IMAGE:latest
                docker push $DOCKER_IMAGE:latest
                '''
            }
        }
        stage('Update Kubernetes Manifest') {
            steps {
                sh '''
                sed -i "s|image: .*|image: $DOCKER_IMAGE:${BUILD_NUMBER}|" deployment.yaml
                git add deployment.yaml
                git commit -m "Updated image to $DOCKER_IMAGE:${BUILD_NUMBER}"
                git push origin main
                '''
            }
        }
        stage('Trigger ArgoCD Sync') {
            steps {
                sh '''
                argocd login $ARGOCD_SERVER --auth-token $ARGOCD_AUTH_TOKEN --insecure
                argocd app sync argocd-test-app
                '''
            }
        }
    }
}

