pipeline {
    agent {
        docker {
            image 'maven:3.9.9-eclipse-temurin-21'
        }
    }

    stages {

        stage('Check Tools') {
            steps {
                sh '''
                echo "Checking Docker..."
                    docker -v || exit 1

                echo "Checking Docker daemon..."
                    docker info || exit 1
                    '''
                }
            }

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

            stage('Build Image') {
                steps {
                    sh 'mvn spring-boot:build-image -Dspring-boot.build-image.imageName=myapp:latest'
                }
            }

            stage('Run Container') {
                steps {
                    sh '''
                    docker stop myapp || true
                    docker rm myapp || true
                    docker run --name myapp -d -p 8080:8080 myapp:latest
                    '''
                }
            }
    }

    post {
        always {
            sh 'echo Checking target folder...'
            sh 'ls -l target || true'
    
            junit 'target/surefire-reports/*.xml'
    
            jacoco(
                execPattern: 'target/jacoco.exec',
                classPattern: 'target/classes',
                sourcePattern: 'src/main/java'
            )
            
        archiveArtifacts artifacts: 'target/spring-petclinic-4.0.0-SNAPSHOT.jar', fingerprint: true
        archiveArtifacts artifacts: 'target/site/jacoco/**/*', fingerprint: true
        archiveArtifacts artifacts: 'target/surefire-reports/**/*', fingerprint: true
        }
        }
}
