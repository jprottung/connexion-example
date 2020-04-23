pipeline {
  agent any
  stages {
    stage('Lint') {
      agent {
        docker {
            image 'hadolint/hadolint:latest-debian'
        }
      }
      steps {
          sh 'hadolint Dockerfile'
      }
    }
    stage('Build') {
      steps {
        sh 'echo "Hello World"'
      }
    }
  }
}