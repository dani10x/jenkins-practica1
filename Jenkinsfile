pipeline {
    agent any

    tools {
        maven 'LaclaMaven'
        jdk 'JDK_17'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                bat 'mvn clean package'
            }
        }

        stage('SonarQube Analysis') {
            environment {
                scannerHome = tool 'Scanner'
            }
            steps {
                withSonarQubeEnv('Local Sonar') {
                    bat "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=pruebas -Dsonar.sources=src/main -Dsonar.java.binaries=target/classes"
                }
            }
        }

        stage('Quality Gate') {
            steps {
                script {
                    timeout(time: 10, unit: 'MINUTES') {
                        waitForQualityGate abortPipeline: true
                    }
                }
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: '**/target/*.jar', allowEmptyArchive: true
        }
    }
}