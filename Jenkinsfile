pipeline {
    agent any

    tools {
        maven 'Maven'              // Jenkins → Manage Jenkins → Tools → Maven ka NAME
        jdk 'JDK17'                // Agar JDK configured hai (optional)
    }

    environment {
        SONAR_PROJECT_KEY = "petclinic"
        SONAR_PROJECT_NAME = "petclinic"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/SAMARDHAWAN17/Petclinic.git'
            }
        }

        stage('Build & Test') {
            steps {
                sh 'mvn clean verify'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                script {
                    // 🔥 SonarScanner TOOL ka NAME (same as Jenkins Tools)
                    def scannerHome = tool 'SonarScanner'

                    withSonarQubeEnv('SonarQube') {
                        sh """
                        ${scannerHome}/bin/sonar-scanner \
                        -Dsonar.projectKey=${SONAR_PROJECT_KEY} \
                        -Dsonar.projectName=${SONAR_PROJECT_NAME} \
                        -Dsonar.sources=src \
                        -Dsonar.java.binaries=target
                        """
                    }
                }
            }
        }
    }

    post {
        success {
            echo "✅ Pipeline SUCCESSFUL"
        }
        failure {
            echo "❌ Pipeline FAILED"
        }
    }
}
