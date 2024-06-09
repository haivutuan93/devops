pipeline {
  agent any
  stages {
    stage('Build') {
      steps {
        script {
          sh 'mvn clean install'
        }

      }
    }

    stage('Deploy') {
      steps {
        script {
          sh 'java -jar target/demo-0.0.1-SNAPSHOT.jar'
        }

      }
    }

  }
}