pipeline {
    agent any 
    tools {nodejs "nodejs"}

     options {
        timeout(time: 10, unit: 'MINUTES')
     }
    environment {
    ACR_NAME = "dianeacr"
    registyUrl = "dianeacr.azurecr.io"
    IMAGE_NAME = "nodejswebapp"
    IMAGE_TAG = "v1.0.0"
    registryCredential  = "acr-registry"
    }
    stages { 
        stage('SCM Checkout') {
            steps{
           git branch: 'main', url: 'https://github.com/ooghenekaro/nodejs-webapp-2.git'
            }
        }
        // run sonarqube test
        stage('Run Sonarqube') {
            environment {
                scannerHome = tool 'sonarqubescanner';
            }
            steps {
              withSonarQubeEnv(credentialsId: 'jenkins-sonar-acr', installationName: 'sonarqube') {
                sh "${scannerHome}/bin/sonar-scanner"
              }
            }
        }
       // Building Docker Image 
       stage ('Build Docker image') {
        steps {
                script {
                    //dockerImage = docker.build registryUrl
                 def dockerImage = docker.build("${ACR_NAME}.azurecr.io/${IMAGE_NAME}:${IMAGE_TAG}", '.') 
                }
            }
       } 
    // Uploading Docker images into ACR
        stage('Upload Image to ACR') {
         steps{   
             script {
                 docker.withRegistry( "http://${ACR_NAME}.azurecr.io", registryCredential ) {
               // dockerImage.push()
              sh " docker push ${ACR_NAME}.azurecr.io/${IMAGE_NAME}:${IMAGE_TAG}"
                  }
              }
         }
      }
    }
}
