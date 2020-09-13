pipeline {
    environment {
        registry = "sbmvirdi/capstone"
        registryCredential = 'sbmvirdi'
    }
     agent any
     stages {
         stage('Build') {
              steps {
                  sh 'echo Building...'
              }
         }
         stage('Lint HTML') {
              steps {
                  sh 'tidy -q -e *.html'
              }
         }
         stage('Build Docker Image') {
              steps {
                  sh 'docker build -t capstone .'
              }
         }
         stage('Push Docker Image') {
              steps {
                  withDockerRegistry(registry:[url: "", credentialsId: "dockerid"]) {
                      sh "docker tag capstone sbmvirdi/capstone"
                      sh 'docker push sbmvirdi/capstone'
                  }
              }
         }
         stage('Deploying') {
              steps{
                  echo 'Deploying to AWS...'
                  withAWS(credentials: 'sbmvirdi', region: 'ap-south-1') {
                      sh "aws eks --region ap-south-1 update-kubeconfig --name udacity-eks1"
                      sh "kubectl config use-context arn:aws:eks:ap-south-1:738141897248:cluster/udacity-eks1"
                      sh "aws sts get-caller-identity"
                      //sh "kubectl set image deployment/capstone capstone=fairoza/capstone:latest"
                      //sh "kubectl apply -f cloudformation/aws-auth-cm.yaml"
                      sh "kubectl apply -f deployment/deployment.yaml"
                      sh "kubectl get nodes"
                      sh "kubectl get deployment"
                      sh "kubectl get pod -o wide"
                      sh "kubectl get service/capstone"
                  }
              }
        }
        stage("Cleaning up") {
              steps{
                    echo 'Cleaning up...'
                    sh "docker system prune"
              }
        }
     }
}