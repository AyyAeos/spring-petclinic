pipeline {
    agent any

    environment {
        PATH = "/usr/local/bin:/usr/bin:/bin:/opt/homebrew/bin"
    }

    tools {
        maven 'Maven3'
    }

    stages {

        stage('Build') {
            steps {
                bat 'mvn clean package -DskipTests'
            }
        }

        stage('Test') {
            steps {
                bat 'mvn test'
            }
        }

        stage('Docker Build') {
            steps {
                bat 'which docker'
                bat 'docker version'
                bat 'docker build -t myapp:latest .'
            }
        }

        stage('Run Container') {
            steps {
                bat 'docker run -d -p 8080:8080 myapp:latest'
            }
        }
    }

    post {
        always {
            junit 'target/surefire-reports/*.xml'
        }
    }
}
