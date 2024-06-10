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
                // Copy script vào container
                writeFile file: 'start_app.sh', text: '''
                    #!/bin/bash
                    LOG_DIR="/var/log/jenkins"
                    mkdir -p $LOG_DIR
                    nohup java -jar target/demo-0.0.1-SNAPSHOT.jar --server.port=80 > $LOG_DIR/app.log 2>&1 &
                '''
                // Đảm bảo script có quyền thực thi
                sh 'chmod +x start_app.sh'
                // Chạy script để khởi động ứng dụng
                sh './start_app.sh'
            }
        }
    }
}
