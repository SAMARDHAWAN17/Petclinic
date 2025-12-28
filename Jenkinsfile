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
        IMAGE_NAME = "<DOCKERHUB_USERNAME>/petclinic"
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

        stage('SonarQube Anal
