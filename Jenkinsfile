pipeline {
    agent any

    environment {
        DOCKER_CREDENTIALS_ID = 'haivt-dockerhub-user-pass	' 
        DOCKER_IMAGE_NAME = 'haivutuan93/demo-java-app'
        DOCKER_IMAGE_TAG = 'latest'
        KUBECONFIG_CREDENTIALS_ID = 'kubeconfig-docker-desktop'
        CLUSTER_NAME = 'docker-desktop'
        //AWS_REGION = 'your-aws-region'
    }

    stages {
        stage('Load Env') {
            steps {
                script {
                    // Load file .env
                    def envFileContent = readFile '.env'
                    def envVars = envFileContent.split('\n')
                    for (line in envVars) {
                        if (line.trim()) {
                            def parts = line.split('=')
                            def key = parts[0].trim()
                            def value = parts[1].trim()
                            env."${key}" = value
                        }
                    }
                }
            }
        }

        stage('Build') {
            steps {
                sh 'echo Building...'
                sh 'mvn clean install'
            }
        }

        stage('Build Image') {
            steps {
                script {
                    sh 'echo Building Docker Image...'
                    sh """
                    docker build -t ${DOCKER_IMAGE_NAME}:${DOCKER_IMAGE_TAG} --build-arg JAR_FILE=target/demo-0.0.1-SNAPSHOT.jar .
                    """
                }
            }
        }

        stage('Push Image') {
            steps {
                script {
                    docker.withRegistry('', DOCKER_CREDENTIALS_ID) {
                        sh 'echo Pushing Docker Image...'
                        sh "docker push ${DOCKER_IMAGE_NAME}:${DOCKER_IMAGE_TAG}"
                    }
                }
            }
        }

        stage('Deploy to K8s') {
            steps {
                script {
                    withCredentials([file(credentialsId: KUBECONFIG_CREDENTIALS_ID, variable: 'KUBECONFIG')]) {
                        sh """
                        export KUBECONFIG=${KUBECONFIG}
                        kubectl config use-context ${CLUSTER_NAME}
                        kubectl set image deployment/demo demo=${DOCKER_IMAGE_NAME}:${DOCKER_IMAGE_TAG}
                        kubectl rollout status deployment/demo
                        """
                    }
                }
            }
        }
    }
}
