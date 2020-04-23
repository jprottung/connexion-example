pipeline {
  agent any
  environment {
    registry = "jprottung/udacity-capstone-connexion"
    registryCredential = "6850ffdf-418c-4ecf-976c-5c11d3083ba9"
    dockerImage = ""
  }
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
    stage('Build Image') {
      steps {
        script {
          dockerImage = docker.build(registry + ":${env.BUILD_ID}")
        }
      }
    }
    stage('Deploy Image') {
      steps{
        script {
          docker.withRegistry( '', registryCredential ) {
            dockerImage.push()
            dockerImage.push("latest")
          }
        }
      }
    }
    stage('Rollout Image') {
      steps {
        script {
          sh 'kubectl set image deployment/connexion-deployment connexion=' + registry + ':${env.BUILD_ID}'
        }
      }
    }
  }
}