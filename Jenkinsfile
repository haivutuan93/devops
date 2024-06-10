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
                // Tạo thư mục log nếu chưa tồn tại
                sh 'mkdir -p $LOG_DIR'
                sh 'pwd'
                // Chạy ứng dụng với nohup và lưu log vào volume đã mount
                sh 'nohup java -jar target/demo-0.0.1-SNAPSHOT.jar --server.port=80 > $LOG_DIR/app.log 2>&1 &'
            }
        }
    }
}
