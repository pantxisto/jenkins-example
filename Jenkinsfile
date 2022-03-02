pipeline {

  environment {
    registry = "docker.myweb.example.com/justme/myweb"
    dockerImage = ""
  }

  agent any

  stages {

    stage('Checkout Source') {
      steps {
        git 'https://github.com/pantxisto/jenkins-example.git'
      }
    }

    stage('Build image') {
      agent {
        docker.withRegistry('https://docker.myweb.example.com', 'docker-registry-id') {
          dockerImage = docker.build("${env.registry}:${env.BUILD_NUMBER}")
        }
      }
      steps{
        sh 'echo Building image...'
      }
    }

    stage('Push Image') {
      agent {
        dockerImage.push()
      }
      steps{
        sh 'echo Pushing image...'
      }
    }

    stage('Deploy App') {
      steps {
        script {
          kubernetesDeploy(configs: "myweb.yaml", kubeconfigId: "mykubeconfig")
        }
      }
    }

  }

}
