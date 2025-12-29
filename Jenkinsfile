pipeline {
    agent any

    tools {
        maven 'Maven3'
        jdk 'Java17'
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    credentialsId: 'github-token',
                    url: 'https://github.com/SAMARDHAWAN17/Petclinic.git'
            }
        }

        stage('Maven Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                script {
                    // Jenkins → Tools → SonarQube Scanner
                    def scannerHome = tool 'SonarScanner'

                    // Jenkins → Manage Jenkins → System → SonarQube servers
                    withSonarQubeEnv('sonarqube') {
                        sh """
                        ${scannerHome}/bin/sonar-scanner \
                        -Dsonar.projectKey=petclinic \
                        -Dsonar.projectName=petclinic \
                        -Dsonar.sources=src \
                        -Dsonar.java.binaries=target
                        """
                    }
                }
            }
        }
    }
}
