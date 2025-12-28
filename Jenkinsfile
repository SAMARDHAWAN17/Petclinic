pipeline {
    agent any

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
                sh 'mvn clean package'
            }
        }

        stage('SonarQube Analysis (Docker-based)') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh '''
                    docker run --rm \
                      -e SONAR_HOST_URL=$SONAR_HOST_URL \
                      -e SONAR_LOGIN=$SONAR_AUTH_TOKEN \
                      -v "$(pwd):/usr/src" \
                      sonarsource/sonar-scanner-cli:4.8 \
                      -Dsonar.projectKey=petclinic \
                      -Dsonar.projectName=petclinic \
                      -Dsonar.sources=src \
                      -Dsonar.java.binaries=target
                    '''
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
                --timeout 5m
