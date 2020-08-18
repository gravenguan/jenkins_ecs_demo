pipeline {
  environment {
    registry = "gravenguan/jenkins_ecs_cicd"
    registryCredential = 'dockerhub'
    dockerImage = ''
    BUILD_NUMBER = '1.0.3'
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
    stage('Start ecs services...') {
      steps{
        sh '''
            docker rmi $registry:$BUILD_NUMBER
            sed -e "s;%BUILD_NUMBER%;${BUILD_NUMBER};g" heran-test.json
            aws ecs register-task-definition --family heran-test --cli-input-json file://heran-test.json
            revision='aws ecs describe-task-definition --task-definition heran-test | egrep "revision" | tr "/" " " | awk '{print $2}' | sed 's/"$//'
            aws ecs update-service --cluster heran-test --service heran-service --task-definition heran-test:${revision} --desired-count 1
        '''
      }
    }
  }
}