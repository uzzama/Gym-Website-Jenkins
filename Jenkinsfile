pipeline {
   agent any

    stages{
        stage('Build Docker Image'){
        steps{
        sh "docker build . -t uzzama/gym-website:${env.BUILD_ID}"
     }
}

        stage('Dockerhub Push'){

        steps{
        withCredentials([string(credentialsId: 'uzzama_docker_cred', variable: 'dockerHubPwd')]) {
        sh "docker login -u uzzama -p ${dockerHubPwd}"
        sh "docker push uzzama/gym-website:${env.BUILD_ID}"
        }
     }
   }
       stage('Docker Remove Image') {
      steps {
        sh "docker rmi uzzama/gym-website:${env.BUILD_NUMBER}"
      }
    }
       stage('Apply Kubernetes Files') {
      steps {
          withKubeConfig([credentialsId: 'azure_kubeconfig']) {
          sh 'cat deployment.yaml | sed "s/{{BUILD_NUMBER}}/$BUILD_NUMBER/g" | kubectl apply -f -'
          sh 'kubectl apply -f service.yaml'
        }
      }
  }

}

}
