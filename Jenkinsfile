pipeline {
   agent any

   environment {
     // You must set the following environment variables
     DOCKERHUB_CREDENTIALS = credentials('docker-credentials') 
     ORGANIZATION_NAME = "aranga-nana"
     YOUR_DOCKERHUB_USERNAME = "aranga" 

     SERVICE_NAME = "fleetman-api-gateway"
     REPOSITORY_TAG="aranga/gateway-jk:${BUILD_ID}"
   }

   stages {
      stage('Preparation') {
         steps {
            cleanWs()
            git credentialsId: 'GitHub', url: "https://github.com/aranga-nana/fleetman-api-gateway.git"
         }
      }
      stage('Build') {
         steps {
            sh '''./mvnw clean package'''
         }
      }
      stage('Docker Login') {
         steps {
          sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'  
         }
         
      }
      stage('Build and Push Image') {
         steps {
           sh 'docker image build -t ${REPOSITORY_TAG} .'
         }
      }

      stage('Deploy to Cluster') {
          steps {
                    sh 'envsubst < ${WORKSPACE}/deploy.yaml | kubectl apply -f -'
          }
      }
   }
}
