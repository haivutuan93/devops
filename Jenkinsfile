pipeline {
    agent any
    
    environment {
        properties = readProperties file: 'Jenkins.properties'
        DOCKER_CREDENTIALS_ID = properties['DOCKER_CREDENTIALS_ID']
        DOCKER_IMAGE = properties['DOCKER_IMAGE']
        EKS_CLUSTER_NAME = properties['EKS_CLUSTER_NAME']
        EKS_REGION = properties['EKS_REGION']
        KUBECONFIG_PATH = properties['KUBECONFIG_PATH']
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
                    sh 'docker build -t $DOCKER_IMAGE:$BUILD_NUMBER .'
                    
                    echo "Logging into Docker Hub..."
                    withCredentials([usernamePassword(credentialsId: DOCKER_CREDENTIALS_ID, passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')]) {
                        sh 'echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin'
                    }
                    
                    echo "Pushing Docker image to Docker Hub..."
                    sh 'docker push $DOCKER_IMAGE:$BUILD_NUMBER'
                }
            }
        }

        stage('Deploy to EKS') {
            steps {
                script {
                    echo "Deploying to EKS..."
                    sh '''
                        aws eks --region $EKS_REGION update-kubeconfig --name $EKS_CLUSTER_NAME --kubeconfig $KUBECONFIG_PATH
                        kubectl set image deployment/your-deployment your-container=$DOCKER_IMAGE:$BUILD_NUMBER --kubeconfig $KUBECONFIG_PATH
                    '''
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
