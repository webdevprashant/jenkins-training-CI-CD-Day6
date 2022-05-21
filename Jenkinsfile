       
pipeline {
           
    agent {
        label "slave1"
    }
       
    stages {
        stage('SCM') {
            steps {
                git url: 'https://github.com/vimallinuxworld13/jenkins-docker-maven-java-webapp.git',branch: 'master'
            }
        }
        
        
          stage(" Maven Clean Package"){
               steps {
                    sh "mvn clean package"
               }
      
    } 
        
        stage('Build Docker Image'){
             steps {
                sh 'sudo docker build -t vimal13/java-web-app .'
             }
    }
    
    
     stage('Push Docker Image'){
         steps {
        withCredentials([string(credentialsId: 'Docker_Hub_Pwd', variable: 'Docker_Hub_Pwd')]) {
          sh "sudo docker login -u vimal13 -p ${Docker_Hub_Pwd}"
        }
        sh 'sudo docker push vimal13/java-web-app'
     }
     }
     
     
     
      stage('Run Docker Image In Dev Server'){
          steps {
        sh 'sudo docker rm -f java-web-app || true'
        sh 'sudo docker run  -d -p 8080:8080 --name java-web-app vimal13/java-web-app'
        
        // http://hostIP:8080/java-web-app/
        
        sshagent(['DOCKER_SERVER_SSH_KEY']) {
        sh 'ssh -o StrictHostKeyChecking=no ec2-user@18.141.194.51 sudo docker stop java-web-app || true'
        sh 'ssh  ec2-user@18.141.194.51 sudo docker rm java-web-app || true'
        sh 'ssh  ec2-user@18.141.194.51 sudo docker rmi -f  $(sudo docker images -q) || true'
        sh "ssh  ec2-user@18.141.194.51 sudo docker run  -d -p 8080:8080 --name java-web-app vimal13/java-web-app"
    
       }
          }
          
          }
       
    stage('Final Test'){
          steps { 
     sh 'curl --silent http://18.141.194.51:8080/java-web-app/ | grep India'
          }
    }
    

stage("Stage with input") {
    steps {
     
        script {
            Boolean userInput = input(id: 'Proceed1', message: 'Promote build?', parameters: [[$class: 'BooleanParameterDefinition', defaultValue: true, description: '', name: 'Please confirm you agree with this']])
            echo 'userInput: ' + userInput

            if(userInput == true) {
                // do action
            } else {
                // not do action
                echo "Action was aborted."
            }

        }    
    }  
}


    

}
}

