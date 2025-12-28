pipeline {
    agent any

    // 🛡️ SAFETY OPTIONS (MOST IMPORTANT)
    options {
        timeout(time: 12, unit: 'MINUTES')   // build auto-kill
        disableConcurrentBuilds()            // ek time pe ek hi build
        buildDiscarder(logRotator(numToKeepStr: '5'))
    }

    tools {
        maven 'Maven3'
        jdk 'Java17'
    }

    environment {
        MAVEN_OPTS = "-Xmx512m"   // memory limit
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
                sh '''
                mvn clean package -DskipTests=false
                '''
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

        stage('Trivy Security Scan (SAFE MODE)') {
            steps {
                sh '''
                trivy fs \
                --skip-dirs .git,target,.m2,node_modules \
                --severity HIGH,CRI
