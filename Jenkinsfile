pipeline {
  environment {
    registry = "gravenguan/jenkins_ecs_cicd"
    registryCredential = 'dockerhub'
    dockerImage = ''
    BUILD_NUMBER = '1.0.2'
  }
  agent any
  stages {
    stage('Cloning Git') {
      steps {
        git branch: 'master',
        credentialsId: 'heran_github',
        url: 'https://github.com/gravenguan/jenkins_ecs_demo.git'
      }
    }
    stage('Building image') {
      steps{
        script {
          dockerImage = docker.build registry + ":$BUILD_NUMBER"
        }
      }
    }
    stage('Deploy Image') {
      steps{
        script {
          docker.withRegistry( '', registryCredential ) {
            dockerImage.push()
          }
        }
      }
    }
    stage('Remove Unused docker image') {
      steps{
        sh "docker rmi $registry:$BUILD_NUMBER"
      }
    }
  }
}