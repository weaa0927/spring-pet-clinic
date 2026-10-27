pipeline {
    agent any
    tools {
        jdk 'jdk21'
        maven 'M3'
    }
    environment {
        DOCKERHUB_CRED = credentials('Dockerhub')
        KUBECONFIG = credentials('kubeconfig')
    }

    stages {
        // Github로 부터 소스코드 다운로드
        stage('Git Clone') {
            steps {
                echo 'Git Clone'
                git url: 'https://github.com/weaa0927/spring-pet-clinic.git',
                branch: 'main'
            }
        }
        // Maven을 이용한 Build
        stage('Maven Build') {
            steps {
                echo 'Maven Build'
                sh 'mvn -Dmaven.test.failure.ignore=true clean package'
            }
        }
        // Docker Image 생성
        stage('Docker Image Create') {
            steps {
                echo 'Docker Image Create'
                sh '''
                docker build -t spring-petclinic:${BUILD_NUMBER} .
                docker tag spring-petclinic:${BUILD_NUMBER} weaa0927/petclinic:latest
                '''
            }
        }
        // Docker hub Image Push
        stage('Docker Image Push') {
            steps {
                echo 'Docker Image Push'
                sh 'echo ${DOCKERHUB_CRED_PSW} | docker login -u ${DOCKERHUB_CRED_USR} --password-stdin'
                sh 'docker image push weaa0927/petclinic:latest'
            }
        }
        // SSH Publish 대신: kubectl로 쿠버네티스에 배포 (강사님 방향, 교재 외)
        stage('Deploy') {
            steps {
                echo 'Deploy'
                sh 'kubectl apply -f k8s/petclinic-deploy.yaml'
                sh 'kubectl apply -f k8s/petclinic-svc-ing.yaml'
                sh 'kubectl rollout restart deployment/petclinic -n was'
            }
        }
    }
    post {
        always {
            sh 'docker rmi -f spring-petclinic:${BUILD_NUMBER} || true'
        }
    }
}
