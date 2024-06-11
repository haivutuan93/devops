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
                // stop.sh
                writeFile file: 'stop_app.sh', text: '''
                    #!/bin/bash
                    
                    # Dừng ứng dụng Java cũ nếu đang chạy
                    OLD_PID=$(ps -ef | grep '[j]ava -jar target/demo-0.0.1-SNAPSHOT.jar' | awk '{print $2}')
                    if [ -n "$OLD_PID" ]; then
                        echo "Stopping old application with PID $OLD_PID"
                        kill -9 $OLD_PID
                    else
                        echo "No old application running"
                    fi
                '''

                sh 'chmod +x stop_app.sh'
                sh './stop_app.sh'
                
                // Wait 5s
                sh 'sleep 5'
                
                // start.sh
                writeFile file: 'start_app.sh', text: '''
                    #!/bin/bash
                    LOG_DIR="/var/log/jenkins"
                    
                    # Tạo thư mục log nếu chưa tồn tại
                    mkdir -p $LOG_DIR

                    # Chạy ứng dụng mới với nohup và disown để chạy ngầm
                    nohup java -jar target/demo-0.0.1-SNAPSHOT.jar --server.port=80 > $LOG_DIR/app.log 2>&1 &
                '''

                sh 'chmod +x start_app.sh'
                sh './start_app.sh'
                
                // Kiểm tra log để đảm bảo ứng dụng đang khởi động
                sh 'sleep 2'
            }
        }
    }
}
