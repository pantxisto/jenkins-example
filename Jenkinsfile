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
          echo data
          echo data.spec.template.spec.containers
          data.spec.template.spec.containers[0].image = imagename + ":$BUILD_NUMBER"
          writeYaml file: filename, data: data
          kubernetesDeploy(configs: './prueba.yaml', kubeconfigId: kubeconfigCredential)
        }
      }
    }

  }

}
