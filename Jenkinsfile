pipeline {
    agent {  label "master"    }
    
    stages {
        // Step 1
        stage('SCM') {
                steps {
                    git 'https://github.com/saikrishna123-wo/jenkins-training-CI-CD-Day6.git'
                }        
        }
        // Step 2
        stage('Build by Maven') {
                steps {
                    sh 'mvn clean package'
                }
        }
        
        // Step 3
        stage('Build docker image') {
                steps {
                    sh "sudo docker build -t pavangottipati/pavan_java:${BUILD_NUMBER} ."
                }
        }
        
        // Step 4
        stage('Push docker image') {
                steps {
                    withCredentials([string(credentialsId: 'alekhya@9020', variable: 'VAR_FOR_DOCKERPASS')]) {
                    sh "sudo docker login -u pavangottipati -p $VAR_FOR_DOCKERPASS"
                    }
                    sh "sudo docker push pavangottipati/pavan_java:${BUILD_NUMBER}"
                }
        }
        
        // Step 5 
        stage('Deploy Java App in  Dev Env') {
                steps {
                        sh "sudo docker rm -f myjavaappdevenv"
                        sh "sudo docker run  -d -p 1222:8080 --name myjavaappdevenv webdevprashant/javaapp-day6:${BUILD_NUMBER}"
                }
        }
        
        // Step 6  in  Redhat CLI 1 
        stage('Deploy Java in QA/Test Env') {
            steps {
                    // sshagent(['QA_ENV_SSH_CRED']) {
                        // sh "ssh root@192.168.43.229 docker rm -f myjavaapp"
                        // sh "ssh root@192.168.43.229 docker run  -d -p 8080:8080 --name myjavaapp webdevprashant/javaapp-day6:${BUILD_NUMBER}"
            sh "sudo docker rm -f myjavaappqatestenv"            
            sh "sudo docker run  -d -p 1223:8080 --name myjavaappqatestenv webdevprashant/javaapp-day6:${BUILD_NUMBER}"           
                    // }
            }
        }
            
        stage('QAT Test') {
            steps {
        	// bcz tomcat take some sec. to display data , so apply some delay here        
                retry(30) {
                    sh 'curl --silent http://192.168.43.56:1223/java-web-app/ |  grep India'
                }   
            }
        }
        
        // Step  in Redhat 8 CLI 2
        stage('Deploy webAPP in Prod Env') {
            steps {
               
            //   sshagent(['QA_ENV_SSH_CRED']) {                    
                        // sh "ssh root@192.168.43.229 docker rm -f myjavaapp"
                        // sh "ssh root@192.168.43.229 docker run  -d -p 8080:8080 --name myjavaapp webdevprashant/javaapp-day6:${BUILD_NUMBER}"                   
                // }
                sh "sudo docker rm -f myjavaappprodenv"
                sh "sudo docker run  -d -p 1224:8080 --name myjavaappprodenv webdevprashant/javaapp-day6:${BUILD_NUMBER}"  
            }
        }
    }
}      
