node{
      def dockerImageName= 'certification_udr_$JOB_NAME'
     
      stage('SCM Checkout'){
         git 'https://github.com/KnackCloud/certificate-upload.git'
      }
      stage('Build'){
         // Get maven home path and build
         def mvnHome =  tool name: 'Maven 3.5.4', type: 'maven'   
         sh "${mvnHome}/bin/mvn package -Dmaven.test.skip=true"
      }      
      stage('Build Docker Image'){         
           sh "docker build -t ${dockerImageName} ."
      }  
   
      stage('Publish Docker Image'){
         withCredentials([string(credentialsId: 'dockerpwdkc', variable: 'dockerPWD')]) {
               sh "docker login -u knackc123 -p ${dockerPWD}"
         }
        sh "docker tag ${dockerImageName} knackc123/${dockerImageName}"
        sh "docker push knackc123/${dockerImageName}"
      }
      
    stage('Run Docker Image'){
          def dockerContainerName = 'javadedockerapp_$JOB_NAME_$BUILD_NUMBER'
          def removeOldService = "sudo docker service rm udr"          
          def dockerRun= "sudo docker service create --name udr --publish 80:80 knackc123/${dockerImageName}" 
          
            withCredentials([string(credentialsId: 'deploymentserverpwd', variable: 'dpPWD')]) {  
                  sh "sshpass -p ${dpPWD} ssh -o StrictHostKeyChecking=no root@34.229.191.169 ${removeOldService}"
                  sh "sshpass -p ${dpPWD} ssh -o StrictHostKeyChecking=no root@34.229.191.169 ${dockerRun}"
            }
    }
     
      
         
  }
      
