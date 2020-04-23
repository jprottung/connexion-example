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
          sh 'hadolint Dockerfile | tee -a hadolint_lint.txt'
      }
      post {
          always {
              archiveArtifacts 'hadolint_lint.txt'
          }
      }
    }
    stage('Build') {
      steps {
        sh 'echo "Hello World"'
      }
    }
  }
}