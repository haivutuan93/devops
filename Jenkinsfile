pipeline {
    agent any

    environment {
        JAVA_HOME = "/usr/lib/jvm/java-17-openjdk-amd64"
        PATH = "$JAVA_HOME/bin:$PATH"
    }

    stages {
        stage('Build') {
            steps {
                sh 'mvn clean install'
            }
        }
        
        stage('Deploy') {
            steps {
                sh 'java -jar target/demo-0.0.1-SNAPSHOT.jar'
            }
        }
    }
}