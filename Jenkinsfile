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
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Test + Coverage') {
            steps {
                sh 'mvn test jacoco:report'
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

    post {
        always {
            junit 'target/surefire-reports/*.xml'

            jacoco(
                execPattern: 'target/jacoco.exec',
                classPattern: 'target/classes',
                sourcePattern: 'src/main/java'
            )
            
            archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
        }
    }
}
