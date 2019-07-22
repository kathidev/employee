pipeline {
  agent any
  tools { 
        maven 'Maven'
        jdk 'Java'
  }
  stages {
    stage('Clone repository') {
        /* Let's make sure we have the repository cloned to our workspace... */
      steps {
        checkout scm
      }
    }
    stage('Build') {
      steps {
        sh 'mvn -B -DskipTests clean package'
        sh 'echo $USER'
        sh 'echo whoami'
      }
    }
    stage('Docker Build') {
      steps {
        sh '/usr/bin/docker build -t employee-service .'
      }
    }   
    stage('push image to ECR'){
      steps {
        withDockerRegistry(credentialsId: 'ecr:ap-south-1:aws-credentials', url: 'http://844779914752.dkr.ecr.ap-south-1.amazonaws.com/employee-service') {
          sh 'docker tag employee-service:latest 844779914752.dkr.ecr.ap-south-1.amazonaws.com/employee-service:latest'
         sh 'docker push 844779914752.dkr.ecr.ap-south-1.amazonaws.com/employee-service:latest'
        } 
      }
    }
    stage('deploy to ECR') {
      steps {
        node('eks-master-node'){
          checkout scm
         sh 'kubectl apply -f deployment.yaml' 
         sh 'kubectl apply -f service.yaml' 
        }
      }
    }
  }
}
