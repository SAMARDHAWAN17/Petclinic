pipeline {
    agent any

    // 🛡️ SAFETY – hang nahi hone dega
    options {
        timeout(time: 20, unit: 'MINUTES')
        disableConcurrentBuilds()
        buildDiscarder(logRotator(numToKeepStr: '5'))
    }

    tools {
        maven 'Maven3'
        jdk 'Java17'
    }

    environment {
        IMAGE_NAME = "<samardhawan17>/petclinic"
        IMAGE_TAG  = "latest"
        MAVEN_OPTS = "-Xmx512m"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/SAMARDHAWAN17/Petclinic.git'
            }
        }

        stage('Maven Build & Test') {
            steps {
                sh 'mvn clean package -DskipTests=false'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                script {
                    def scannerHome = tool 'SonarScanner'
                    withSonarQubeEnv('SonarQube') {
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

        stage('Docker Build') {
            steps {
                sh '''
                docker build -t $IMAGE_NAME:$IMAGE_TAG .
                '''
            }
        }

        stage('Trivy Image Scan (SAFE)') {
            steps {
                sh '''
                trivy image \
                --severity HIGH,CRITICAL \
                --timeout 5m \
                --exit-code 0 \
                --no-progress \
                $IMAGE_NAME:$IMAGE_TAG
                '''
            }
        }

        stage('DockerHub Push') {
            steps {
                withDockerRegistry(credentialsId: 'dockerhub-creds', url: '') {
                    sh '''
                    docker push $IMAGE_NAME:$IMAGE_TAG
                    '''
                }
            }
        }
    }

    post {
        success {
            echo "✅ CI/CD pipeline completed successfully"
        }
        failure {
            echo "❌ Pipeline failed"
        }
        always {
            echo "📦 Pipeline finished"
        }
    }
}
