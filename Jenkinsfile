pipeline {    
    agent any

    environment {
        MYSQL_URL="jdbc:mysql://mysql-db:3306/petclinic"
        MYSQL_USER="petclinic"
        MYSQL_PASS="petclinic"
        MYSQL_DB="petclinic"
        POSTGRES_DB="petclinic"
        POSTGRES_USER="petclinic"
        POSTGRES_PASS="petclinic"
        IMG_NM = "petclinic-app"
    }
    
    stages {
        stage('Init and Build Project') {
            parallel {
                // Check Docker Deamon is running
                stage('Check Tools') {
                    steps {
                        bat '''
                        echo "Checking Docker Environment"
                            docker info || { echo "Docker daemon is not running. ";exit 1; }                '''
                        }
                    }

                // start postgres database
                stage('Start Database') {
                    steps {
                        bat 'docker compose down'
                        bat 'docker rm -f petclinic-postgres'
                        bat 'docker compose up -d postgres'
                    }
                }

                // build project
                stage('Build') {
                    steps {
                        bat './mvnw clean package -DskipTests'
                    }
                }
            }
        }

        stage('Test and Quality Analysis') {
            parallel {
                stage('Test and Coverage Report') {
                    steps {
                        bat './mvnw test jacoco:report'
                    }
                }

                stage('SonarQube Analysis') {
                    steps {
                        withSonarQubeEnv('petclinic') {
                            sh "./mvnw sonar:sonar -Dsonar.projectKey=petclinic"
                        }
                    }
                }
            }
        }
        
        stage('Sonar Quality Report') {
            steps {
                waitForQualityGate abortPipeline: true
            }
        }

        stage('Docker Build & Run Container') {
            steps {
                bat 'docker compose down'
                bat 'docker compose -f docker-compose.yml up --build pet-clinic -d'
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

            archiveArtifacts artifacts: 'target/spring-petclinic-4.0.0-SNAPSHOT.jar'
            archiveArtifacts artifacts: 'target/site/jacoco/**/*'
            archiveArtifacts artifacts: 'target/surefire-reports/**/*'
        }

        success {
            echo 'Project Build succeeded!'
        }

        failure {
            echo 'Project Build failed!'
        }
    }
}
