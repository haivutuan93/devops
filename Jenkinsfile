pipeline {
    agent any

    environment {
        LOG_DIR = "/var/log/jenkins"
    }

    stages {
        stage('Build') {
            steps {
                sh 'echo $GITHUB_TOKEN'
                sh 'mvn clean install'
            }
        }
        
        stage('Deploy') {
            steps {
                // stop_app.sh.sh
                sh './stop_app.sh'

                // start_app.sh
                sh './start_app.sh'
            }
        }
    }
}
