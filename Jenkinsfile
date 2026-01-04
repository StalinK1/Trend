pipeline {
  agent any

  environment {
    IMAGE_NAME = "stalin15/trend-app"
    DOCKER_CREDS = "stalin15"
    GIT_CREDS = "StalinK1"
  }

  stages {

    stage('Checkout') {
      steps {
        git url: 'https://github.com/StalinK1/Trend.git',
            branch: 'main',
            credentialsId: "${GIT_CREDS}"
      }
    }

    stage('Build Docker Image') {
      steps {
        sh 'docker build -t $IMAGE_NAME:latest .'
      }
    }

    stage('Docker Login') {
      steps {
        withCredentials([usernamePassword(
          credentialsId: "${DOCKER_CREDS}",
          usernameVariable: 'DOCKER_USER',
          passwordVariable: 'DOCKER_PASS'
        )]) {
          sh 'docker login -u $DOCKER_USER -p $DOCKER_PASS'
        }
      }
    }

    stage('Push Docker Image') {
      steps {
        sh 'docker push $IMAGE_NAME:latest'
      }
    }

    stage('Deploy to EKS') {
      steps {
        sh '''
          kubectl apply -f deployment.yaml
          kubectl apply -f service.yaml
        '''
      }
    }
  }

  post {
    success {
      echo '✅ Pipeline executed successfully!'
    }
    failure {
      echo '❌ Pipeline failed. Check logs.'
    }
  }
}
