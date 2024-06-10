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
                // Chạy ứng dụng với nohup
                sh 'nohup java -jar target/demo-0.0.1-SNAPSHOT.jar --server.port=80 > app.log 2>&1 &'
                // Hiển thị log trong console của Jenkins
                sh 'tail -f app.log'
            }
        }
    }
}