pipeline {
  agent any

  tools {
    maven 'maven3'
  }

  environment {
    IMAGE_NAME = "samardhawan17/petclinic"
    IMAGE_TAG  = "${BUILD_NUMBER}"
    K8S_IP     = "YOUR_K8S_PUBLIC_IP"
  }

  stages {

    stage('Checkout Code') {
      steps {
        git branch: 'main',
            url: 'https://github.com/SAMARDHAWAN17/Petclinic.git',
            credentialsId: 'github-creds'
      }
    }

    stage('Maven Build') {
      steps {
        sh 'mvn clean package -DskipTests'
      }
    }

    stage('SonarQube Scan') {
      environment {
        SONAR_TOKEN = credentials('sonar-token')
      }
      steps {
        withSonarQubeEnv('sonarqube') {
          sh '''
            mvn sonar:sonar \
            -Dsonar.projectKey=petclinic \
            -Dsonar.login=$SONAR_TOKEN
          '''
        }
      }
    }

    stage('Docker Build') {
      steps {
        sh '''
          docker build -t $IMAGE_NAME:$IMAGE_TAG .
          docker tag $IMAGE_NAME:$IMAGE_TAG $IMAGE_NAME:latest
        '''
      }
    }

    stage('Trivy Scan') {
      steps {
        sh 'trivy image --exit-code 0 $IMAGE_NAME:$IMAGE_TAG'
      }
    }

    stage('Docker Push') {
      steps {
        withCredentials([usernamePassword(
          credentialsId: 'dockerhub',
          usernameVariable: 'DOCKER_USER',
          passwordVariable: 'DOCKER_PASS'
        )]) {
          sh '''
            echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
            docker push $IMAGE_NAME:$IMAGE_TAG
            docker push $IMAGE_NAME:latest
          '''
        }
      }
    }

    stage('Deploy to Kubernetes') {
      steps {
        sh """
          ssh -o StrictHostKeyChecking=no ubuntu@${K8S_IP} '
            kubectl set image deployment/petclinic petclinic=$IMAGE_NAME:$IMAGE_TAG
            kubectl rollout status deployment/petclinic
          '
        """
      }
    }
  }
}
