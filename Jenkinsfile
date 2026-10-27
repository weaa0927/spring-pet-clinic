pipeline {
  agent any
  environment { IMAGE = 'weaa0927/petclinic' }
  stages {
    stage('Clone') { steps { checkout scm } }
    stage('Build') { steps { sh 'docker build -t $IMAGE:1.0 .' } }
    stage('Push') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'Dockerhub',
            usernameVariable: 'U', passwordVariable: 'P')]) {
          sh 'echo $P | docker login -u $U --password-stdin'
          sh 'docker push $IMAGE:1.0'
        }
      }
    }
  }
}
