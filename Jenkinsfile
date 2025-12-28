pipeline {
    agent any

    tools {
        maven 'Maven3'
        jdk 'Java17'
        sonarScanner 'SonarScanner'
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
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
                withSonarQubeEnv('SonarQube') {
                    sh '''
                    sonar-scanner \
                    -Dsonar.projectKey=petclinic \
                    -Dsonar.projectName=petclinic \
                    -Dsonar.sources=src \
                    -Dsonar.java.binaries=target
                    '''
                }
            }
        }
    }
}
