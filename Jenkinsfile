pipeline {
    agent any
    environment {
        IMAGE_NAME = 'sumanth17121988/sample-app'
        BUILD_TAG = "${BUILD_NUMBER}"  // Unique tag for each build
        GKE_CLUSTER = 'kube-cluster'
        GKE_ZONE = 'us-central1-c'
    }
    stages {
        stage('Checkout Code') {
            steps {
                git 'https://github.com/YOUR_GITHUB_USERNAME/gke-cicd-pipeline.git'
            }
        }
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME:$BUILD_TAG .'
            }
        }
        stage('Authenticate to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'DOCKERHUB_CREDENTIALS', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                sh 'docker push $IMAGE_NAME:$BUILD_TAG'
            }
        }
        stage('Update Kubernetes Manifests') {
            steps {
                sh """
                sed -i 's|IMAGE_PLACEHOLDER|$IMAGE_NAME:$BUILD_TAG|g' k8s/deployment.yaml
                """
            }
        }
        stage('Deploy to GKE') {
            steps {
                sh 'gcloud container clusters get-credentials $GKE_CLUSTER --zone $GKE_ZONE'
                sh 'kubectl apply -f k8s/deployment.yaml'
                sh 'kubectl apply -f k8s/service.yaml'
            }
        }
    }
}
