pipeline {
  environment {
    dockerimagename = "sdsaran/react-app"
    dockerImage = ""
  }
  agent any
  stages {
    stage('Checkout Source') {
      steps {
        git branch: 'main', url:'https://github.com/sdsaran/jenkins-kubernetes.git'
      }
    }
    stage('Build image') {
      steps{
        script {
          dockerImage = docker.build dockerimagename + ":$BUILD_NUMBER"
        }
      }
    }
    stage('Pushing Image') {
      environment {
               registryCredential = 'dockerhub-credentials'
           }
      steps{
        script {
          docker.withRegistry( 'https://registry.hub.docker.com', registryCredential ) {
            dockerImage.push()
            dockerImage.push("latest")
          }
        }
      }
    }
    stage('Deploying React.js container to Kubernetes') {
      steps {
        script {
          def image_id = dockerimagename + ":$BUILD_NUMBER"
          sh "ansible-playbook playbook.yaml --extra-vars \"image_id=${image_id}\""
        }
      }
    }
  }
}
