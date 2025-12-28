pipeline {
    agent any

    tools {
        maven 'Maven3'     // ✅ Jenkins me configured name
        jdk 'Java17'       // ✅ Jenkins me configured name
    }

    environment {
        SONAR_PROJECT_KEY  = "petclinic"
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
                    // ✅ SonarScanner ka EXACT tool name
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
            echo "✅ Jenkins Pipeline SUCCESSFUL"
        }
        failure {
            echo "❌ Jenkins Pipeline FAILED"
        }
    }
}
