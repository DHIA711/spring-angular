pipeline {

    tools {
            maven 'maven'
            nodejs "nodejs"         
          }

    environment {

     springF="ams_back"   
     angularF="ams_front"    
   }

          agent any

    stages{
           
               // Stage: Création de livrable pour spring:back
              stage('Création de .jar ') {

                  steps {

                     sh ' cd ${springF} && mvn clean install -DskipTests'

                              }
                         
              }

               // Stage: Création de /dist pour angular:front
               stage('Création de dist') {

                 steps {

                 sh ' cd ${angularF} && npm install'

                 sh ' cd ${angularF} && npm run build'

                 }
             }


          
          // Build de l’image (spring + angular) +   Déposer les deux images sur DockerHub
          stage("build and push back/front images"){
         
         
                 steps{

                    script {
            
             echo "====++++executing build and push back + front images++++===="
    
          withCredentials([usernamePassword(credentialsId: 'dockerhub_cred', passwordVariable: 'PASS', usernameVariable: 'USER')]){
         
                             sh "docker build -t $USER/ams_back2023 ${springF}/"

                             sh "docker build -t $USER/ams_front2023 ${angularF}/"

                             sh "echo $PASS | docker login -u $USER --password-stdin"

                             sh "docker push $USER/ams_back2023"

                             sh "docker push $USER/ams_front2023"
                         }
                 }
            }
         post{

             always{
                 sh "docker logout"
             }
        
             success{
                 echo "====++++push image execution success++++===="
             }
        
             failure{
                 echo "====++++push image execution failed++++===="
             }
    
            }

          }


          stage('run docker-compose'){

                 steps {

                sh 'docker-compose -f docker-compose.yml up -d'
                    }

             post{
        
             success{
                 echo "====++++Success++++===="
             }
        
             failure{
                 echo "====++++Failed++++===="
               }
    
           }
    
  
            }
        
      }

    }

