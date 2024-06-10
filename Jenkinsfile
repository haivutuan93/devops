pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                sh 'echo $GITHUB_TOKEN'
                sh 'mvn clean install'
            }
        }
        
        stage('Deploy') {
            steps {
                sh 'sudo nohup java -jar target/demo-0.0.1-SNAPSHOT.jar --server.port=80 > app.log 2>&1 &'
            }
        }
    }
}