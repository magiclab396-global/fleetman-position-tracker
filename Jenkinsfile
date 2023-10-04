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
            git credentialsId: 'GitHub1', url: "https://github.com/${ORGANIZATION_NAME}/${SERVICE_NAME}"
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
                  echo `Done pushing image ..11..`
               '''
            }
           
         }
      }

      stage('Deploy to Cluster') {
          steps {
             // withKubeConfig([credentialsId: 'K8sSaToken', serverUrl: "${K8S_API_ENDPOINT}"]){
             //    // sh 'envsubst < ${WORKSPACE}/deploy.yaml | kubectl apply -f -'
             //    //def agentLabel
             //   // if (BRANCH_NAME =~ /^(staging|master)$/)  {
             //   //     agentLabel = "prod"
             //   // } else {
             //   //     agentLabel = "master"
             //   // } 
             // }

             // script{
             //    sh '''  
             //       def text = readFile file: "kustomization.yaml"
             //       text = text.replaceAll("/(?m)^newTag:.*$/", "newTag:${BUILD_ID}") 
             //       export BUILD_ID=${BUILD_ID}
             //       git add . -m "Update app image tag to ${BUILD_ID}"
             //       git push origin master
             //    '''
             //   }

             contentReplace(
                 configs: [
                   fileContentReplaceConfig(
                     configs: [
                       fileContentReplaceItemConfig(
                         search: '(newTag: )([0-9]+)',
                         replace: '${BUILD_ID}',
                         matchCount: 1,
                         verbose: false,
                       )
                     ],
                     fileEncoding: 'UTF-8',
                     lineSeparator: 'Unix',
                     filePath: 'kustomization.yaml'
                   )
                 ]
            )

            // script{
            //     sh '''  
            //        export BUILD_ID=${BUILD_ID}
            //        git add . && git commit -m "Update app image tag to ${BUILD_ID}"
            //        git push origin master
            //     '''
            //    }

             // script {
             //   catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
             //     withCredentials([usernamePassword(credentialsId: "GitHub", passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
             //         def encodedPassword = URLEncoder.encode("$GIT_PASSWORD",'UTF-8')

             //          sh '''  
             //             echo "${GitHub}"
             //             echo "${GIT_USERNAME}"
             //             echo "${GIT_PASSWORD}"
             //             echo "${encodedPassword}"
             //             git config user.email truongpx396@gmail.com
             //             git config user.name jenkins
             //             export BUILD_ID=${BUILD_ID}
             //             git add . && git commit -m "Update app image tag to ${BUILD_ID}"
             //             // git push origin master
             //             git push https://${GIT_USERNAME}:${encodedPassword}@github.com/magiclab396-global/fleetman-position-tracker.git
             //          '''
             //         // sh "git config user.email truongpx396@gmail.com"
             //         // sh "git config user.name jenkins"
             //         // sh "git add ."
             //         // sh "git commit -m 'Triggered Build: ${env.BUILD_NUMBER}'"
             //         // sh "git push https://${GIT_USERNAME}:${encodedPassword}@github.com/${GIT_USERNAME}/example.git"
             //     }
             //   }
             // }

          //   script {
               
          //         withCredentials([string(credentialsId: 'GitHub1', variable: 'GITHUB_TOKEN')]) {
          //             sh '''
          //                 // git config user.email "abhishek.xyz@gmail.com"
          //                 // git config user.name "Abhishek Veeramalla"
          //                 git config user.email "truongpx396@gmail.com"
          //                 git config user.name "jenkins"
          //                 // BUILD_NUMBER=${BUILD_NUMBER}
          //                 // sed -i "s/replaceImageTag/${BUILD_NUMBER}/g" java-maven-sonar-argocd-helm-k8s/spring-boot-app-manifests/deployment.yml
          //                 // git add java-maven-sonar-argocd-helm-k8s/spring-boot-app-manifests/deployment.yml
          //                 // git commit -m "Update deployment image to version ${BUILD_NUMBER}"
          //                 export BUILD_ID=${BUILD_ID}
          //                 git add kustomization.yaml && git commit -m "Update app image tag to ${BUILD_ID}"
          //                 // git push https://${GITHUB_TOKEN}@github.com/${GIT_USER_NAME}/${GIT_REPO_NAME} HEAD:main
          //                 git push https://${GITHUB_TOKEN}@github.com/magiclab396-global/fleetman-position-tracker.git HEAD:master
          //             '''
          //         }
          // }
      }
   }
}
}
