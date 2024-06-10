pipeline {
    agent any

    environment {
        KUBECONFIG_PATH = ''
        DOCKER_CREDENTIALS_ID = ''
        DOCKER_IMAGE = ''
        EKS_CLUSTER_NAME = ''
        EKS_REGION = ''
    }

    stages {
        stage('Initialize') {
            steps {
                script {
                    def properties = readProperties file: 'Jenkins.properties'
                    env.DOCKER_CREDENTIALS_ID = properties['DOCKER_CREDENTIALS_ID']
                    env.DOCKER_IMAGE = properties['DOCKER_IMAGE']
                    env.EKS_CLUSTER_NAME = properties['EKS_CLUSTER_NAME']
                    env.EKS_REGION = properties['EKS_REGION']
                    env.KUBECONFIG_PATH = properties['KUBECONFIG_PATH']
                }
            }
        }

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
                    withCredentials([usernamePassword(credentialsId: env.DOCKER_CREDENTIALS_ID, passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')]) {
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
