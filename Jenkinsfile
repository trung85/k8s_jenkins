pipeline {
  agent any
  stages {
    stage('Docker Build') {
      steps {
        sh "docker build -t minhtrung/podinfo:${env.BUILD_NUMBER} ."
      }
    }
    stage('Docker Push') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'dockerHubPassword', usernameVariable: 'dockerHubUser')]) {
          sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPassword}"
          sh "docker push minhtrung/podinfo:${env.BUILD_NUMBER}"
        }
      }
    }
    stage('Docker Remove Image') {
      steps {
        sh "docker rmi minhtrung/podinfo:${env.BUILD_NUMBER}"
      }
    }
    stage('(DISABLED) Apply Kubernetes Files') {
      steps {
        sh "echo 'cat deployment.yaml | sed \"s/{{BUILD_NUMBER}}/$BUILD_NUMBER/g\" | kubectl apply -f -'"
        sh "echo 'kubectl apply -f service.yaml'"
        // DISABLED
        //   withKubeConfig([credentialsId: 'kubeconfig']) {
        //   sh 'cat deployment.yaml | sed "s/{{BUILD_NUMBER}}/$BUILD_NUMBER/g" | kubectl apply -f -'
        //   sh 'kubectl apply -f service.yaml'
        // }
      }
  }
}
post {
    success {
      slackSend(message: "Pipeline is successfully completed.")
    }
    failure {
      slackSend(message: "Pipeline failed. Please check the logs.")
    }
}
}
