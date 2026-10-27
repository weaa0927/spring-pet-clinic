pipeline {
  agent any
  tools { jdk 'jdk21'; maven 'M3' }
  environment { IMAGE = 'weaa0927/petclinic' }
  stages {
    stage('Clone') { steps { checkout scm } }
    stage('Maven Build') { steps { sh 'mvn -Dmaven.test.failure.ignore=true clean package' } }
    stage('Docker Build') { steps { sh 'docker build -t $IMAGE:latest .' } }
    stage('Docker Push') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'Dockerhub', usernameVariable: 'U', passwordVariable: 'P')]) {
          sh 'echo $P | docker login -u $U --password-stdin'
          sh 'docker push $IMAGE:latest'
        }
      }
    }
    stage('Deploy') {
      steps {
        withCredentials([file(credentialsId: 'kubeconfig', variable: 'KUBECONFIG')]) {
          sh 'kubectl apply -f k8s/'
          sh 'kubectl rollout restart deployment/petclinic -n was'
        }
      }
    }
  }
  post {
    success { echo 'Pipeline 성공' }
    failure { echo 'Pipeline 실패' }
  }
}
