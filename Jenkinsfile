pipeline {
    
    agent {
        docker { 
            image 'node:24.14.1-alpine3.23' 
            // This line is the magic fix:
            args '-v /var/run/docker.sock:/var/run/docker.sock'
        }
    }

    tools {
        maven 'Maven3'
    }

    stages {

        stage('Build') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t myapp:latest .'
            }
        }

        stage('Run Container') {
            steps {
                sh 'docker run -d -p 8080:8080 myapp:latest'
            }
        }
    }
}
