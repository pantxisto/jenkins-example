pipeline {

  environment {
    imagename = "docker.myweb.example.com/justme/myweb"
    registryCredential = 'docker-registry-id'
    dockerImage = ''
  }


  agent any

  stages {

    stage('Cloning Git') {
      steps {
        git 'https://github.com/pantxisto/jenkins-example.git'
      }
    }

    stage('Build image') {
      steps {
        script {
          dockerImage = docker.build imagename + ":$BUILD_NUMBER"
        }
      }
    }

    stage('Push Image') {
      steps{
        script {
          docker.withRegistry( 'https://docker.myweb.example.com', registryCredential ) {
            dockerImage.push()
          }
        }
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
