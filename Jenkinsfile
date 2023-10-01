pipeline {
   agent any

   environment {
     // You must set the following environment variables
     // ORGANIZATION_NAME
     // YOUR_DOCKERHUB_USERNAME (it doesn't matter if you don't have one)

     SERVICE_NAME = "fleetman-position-tracker"
     REPOSITORY_TAG="${YOUR_DOCKERHUB_USERNAME}/${ORGANIZATION_NAME}-${SERVICE_NAME}:${BUILD_ID}"
   }

   stages {
      stage('Preparation') {
         steps {
            cleanWs()
            git credentialsId: 'GitHub', url: "https://github.com/${ORGANIZATION_NAME}/${SERVICE_NAME}"
         }
      }
      stage('Build') {
         steps {
            sh '''mvn clean package'''
         }
      }

      stage('Build and Push Image') {
         steps {
           // sh 'docker image build -t ${REPOSITORY_TAG} .'
            withDockerRegistry([ credentialsId: "DockerHub", url: "" ]){
               sh '''  
                  echo `Build image ...`
                  docker image build -t ${REPOSITORY_TAG} .
                  echo `Push image ...`
                  docker push ${REPOSITORY_TAG}
                  echo `Done pushing image ...`
               '''
            }
           
         }
      }

      stage('Deploy to Cluster') {
          steps {
             withKubeConfig([credentialsId: 'credentialsId', serverUrl: 'https://1AD8F256A49D88C34592D0CB82C22EB7.gr7.ap-southeast-1.eks.amazonaws.com']){
                sh 'envsubst < ${WORKSPACE}/deploy.yaml | kubectl apply -f -'
             }
                    
          }
      }
   }
}
