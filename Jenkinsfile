pipeline {

  environment {
    imagename = "docker.myweb.example.com/prueba"
    registryCredential = 'docker-registry-id'
    kubeconfigCredential = 'mykubeconfig'
    dockerImage = ''
  }


  agent {
      kubernetes {
          defaultContainer 'jnlp'
          yamlFile 'agent-pod.yaml'
      }
  }

  stages {

    stage('Cloning Git') {
      steps {
        git 'https://github.com/pantxisto/jenkins-example.git'
      }
    }

    stage('Build image') {
      steps {
        container('docker') {
          script {
            dockerImage = docker.build imagename + ":$BUILD_NUMBER"
          }
        }
      }
    }

    stage('Push Image') {
      steps{
        container('docker') {
          script {
            docker.withRegistry( 'https://docker.myweb.example.com', registryCredential ) {
              dockerImage.push()
            }
          }
        }
      }
    }

    stage('Deploy App') {
      steps {
        script {
          def filename = './prueba.yaml'
          def data = readYaml file: filename
          println data.spec.template.spec.containers[0][0].image
          data.spec.template.spec.containers[0][0].image = imagename + ":$BUILD_NUMBER"
          println data.spec.template.spec.containers[0][0].image
          kubernetesDeploy(configs: data.toString(), kubeconfigId: kubeconfigCredential)
        }
      }
    }

  }

}
