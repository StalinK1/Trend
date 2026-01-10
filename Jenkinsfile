pipeline {
  agent any

  environment {
    IMAGE_NAME = "stalin15/trend-app"
    
    // AWS credentials configured in Jenkins (type: AWS Credentials)
    AWS_CREDS = "aws-access-creds"
    AWS_REGION = "us-east-2"        // e.g., us-east-1
    EKS_CLUSTER = "trend-cluster" // your EKS cluster name
  }

  stages {

    stage('Checkout') {
      steps {
        git url: 'https://github.com/StalinK1/Trend.git',
            branch: 'main',
            credentialsId: 'StalinK1'
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
          credentialsId: 'stalin15',
          usernameVariable: 'DOCKER_USER',
          passwordVariable: 'DOCKER_PASS'
        )]) {
          sh '''
            echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
          '''
        }
      }
    }

    stage('Push Docker Image') {
      steps {
        sh 'docker push $IMAGE_NAME:latest'
      }
    }

    stage('Configure kubectl for EKS') {
      steps {
        withCredentials([[
          $class: 'AmazonWebServicesCredentialsBinding',
          credentialsId: "terraformaccount",
          accessKeyVariable: 'AWS_ACCESS_KEY_ID',
          secretKeyVariable: 'AWS_SECRET_ACCESS_KEY'
        ]]) {
          sh '''
            aws eks --region $AWS_REGION update-kubeconfig --name $EKS_CLUSTER
            kubectl get nodes
          '''
        }
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
