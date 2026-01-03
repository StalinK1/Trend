```groovy
pipeline {
  agent any

  environment {
    IMAGE = "stalin15/trend-app"
  }

  stages {
    stage('Checkout') {
      steps {
        git 'https://github.com/StalinK1/Trend.git'
      }
    }

    stage('Build Docker Image') {
      steps {
        sh 'docker build -t $IMAGE:latest .'
      }
    }

    stage('Push Image') {
      steps {
        sh '''
          docker login -u yourusername -p yourpassword
          docker push $IMAGE:latest
        '''
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
}
```
