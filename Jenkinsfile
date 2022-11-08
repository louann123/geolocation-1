pipeline {
    agent any
    tools{
        maven 'M2_HOME'
    }
     environment {
    registry = '224630410095.dkr.ecr.us-east-1.amazonaws.com/geolocation_ecr_rep'
    registryCredential = 'jenkins-ecr2'
    dockerimage = ''
  }
    stages {
        stage('Checkout'){
            steps{
                git branch: 'main', url: 'https://github.com/louann123/geolocation-1.git'
                
            }
        }
        stage('Code Build') {
            steps {
                sh 'mvn clean package'
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }
          // Building Docker images
        stage('Building image') {
            steps{
                script {
                    dockerImage = docker.build registry + ":$BUILD_NUMBER"
                }
            }
        }
        // Uploading Docker images into AWS ECR
        stage('Pushing to ECR') {
            steps{
                script {
                    sh 'cat ~/.aws/credentials'
                    sh 'aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 224630410095.dkr.ecr.us-east-1.amazonaws.com'
                    sh 'docker push 224630410095.dkr.ecr.us-east-1.amazonaws.com/geolocation_ecr_rep' + ":$BUILD_NUMBER"
                }
            }
        }
     //deploy the image that is in ECR to our EKS cluster
        stage ("Kube Deploy") {
            steps {
                withKubeConfig(caCertificate: '', clusterName: '', contextName: '', credentialsId: 'eks_credentials2', namespace: '', serverUrl: '') {
               sh "kubectl apply -f eks_deploy_from_ecr.yaml"
                }
            }
        }   
    }
}    

