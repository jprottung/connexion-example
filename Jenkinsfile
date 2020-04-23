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
    stage('Build and Push Docker image') {
      node {
        checkout scm
        def customImage = docker.build("jprottung/udacity-capstone-connexion:${env.BUILD_ID}")
        customImage.push()
        customImage.push('latest')
      }
    }
  }
}