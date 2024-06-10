pipeline {
    agent any

    environment {
        DOCKER_CREDENTIALS_ID = 'dockerhub-credentials'
        DOCKER_IMAGE = 'haivutuan93/java-app-demo'
        EKS_CLUSTER_NAME = 'docker-desktop'
        EKS_REGION = 'us-west-2' // Example value, not used for Docker Desktop
        KUBECONFIG_PATH = '~/.kube/config' // Replace with the actual path, usually ~/.kube/config
    }

    stages {
        stage('Build') {
            steps {
                script {
                    echo "Building the Java application..."
                    sh 'mvn clean install'
                }
            }
        }

        stage('Docker Build and Push') {
            steps {
                script {
                    echo "Building Docker image..."
                    sh "docker build -t ${DOCKER_IMAGE}:${BUILD_NUMBER} ."

                    echo "Logging into Docker Hub..."
                    withCredentials([usernamePassword(credentialsId: DOCKER_CREDENTIALS_ID, passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')]) {
                        sh 'echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin'
                    }

                    echo "Pushing Docker image to Docker Hub..."
                    sh "docker push ${DOCKER_IMAGE}:${BUILD_NUMBER}"
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    echo "Deploying to Kubernetes..."
                    sh """
                        export KUBECONFIG=${KUBECONFIG_PATH}
                        kubectl set image deployment/your-deployment your-container=${DOCKER_IMAGE}:${BUILD_NUMBER}
                    """
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}
