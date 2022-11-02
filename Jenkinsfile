pipeline {
    agent any
    tools{
        maven 'M2_HOME'
    }
     environment {
    registry = '224630410095.dkr.ecr.us-east-1.amazonaws.com/geolocation_ecr_rep'
    registryCredential = 'jenkins-ecr'
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
                    sh 'aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 224630410095.dkr.ecr.us-east-1.amazonaws.com/geolocation_ecr_rep'
                    sh 'docker push 224630410095.dkr.ecr.us-east-1.amazonaws.com/geolocation_ecr_rep:latest'
                }
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }
    }
}