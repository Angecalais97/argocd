pipeline {
    agent any
    environment {
        DOCKER_IMAGE = 's5carles/argocd-app'
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
                docker login -u s5carles -p Angecalais97
                docker push $DOCKER_IMAGE:${BUILD_NUMBER}
                '''
            }
        }
        // stage('Update Kubernetes Manifest') {
        //     steps {
        //         sh '''
        //         sed -i "s|image: .*|image: $DOCKER_IMAGE:${BUILD_NUMBER}|" deployment.yaml
        //         git add deployment.yaml
        //         git commit -m "Updated image to $DOCKER_IMAGE:${BUILD_NUMBER}"
        //         git push origin main
        //         '''
        //     }
        // }
        // stage('Trigger ArgoCD Sync') {
        //     steps {
        //         sh '''
        //         argocd login $ARGOCD_SERVER --auth-token $ARGOCD_AUTH_TOKEN --insecure
        //         argocd app sync argocd-test-app
        //         '''
        //     }
        // }
    }
}

