pipeline {
  agent any
  stages{
  stage('scm'){
    steps{
      echo "git pull my code "
    }
  }
  stage('Deploy'){
    steps{
      echo "Jenkins deploy my code"
      
    }
  }
  stage('test'){
    steps{
      echo "jenkins test my code"
    }
  }
}
}

*************************************************************************************
eND-TO-END PIPELINE

pipeline{
    agent {
        label "linux-slave"
    }
    
    tools {
        // Specify the Maven tool name configured in Jenkins
        maven 'maven'
    }
    
    stages{
        
        stage('SCM'){
            
            steps {
                git 'https://github.com/Mrunal00712/jenkins-docker-maven-java-webapp.git'
            }
            
            
            
        }
        stage('Build by Maven Package') {
            steps {
                script {
                    // Use the configured Maven tool
                    def mvnHome = tool 'maven'
                    sh "${mvnHome}/bin/mvn clean package -U"

                }        
            }
        }
        stage('Build Docker OWN image') {
            steps {
                sh 'sudo docker build -t mrunal0712/java-web-app:v2  .'
            }
        }
        
        stage('Push Image to Docker HUB ') {
            steps {
                withCredentials([string(credentialsId: 'DOCKER_HUB_PWD', variable: 'DOCKER_HUB_PASSWORD')]) {
                sh "sudo docker login -u mrunal0712 -p $DOCKER_HUB_PASSWORD"
                sh "sudo docker push mrunal0712/java-web-app:v2 "
                    }
                
            }
            
        }
        
        stage('Deploy webApp in DEV Env') {
            steps {
                sh 'sudo docker rm -f myjavaapp'
                sh "sudo docker run -d  -p 8080:8080  --name myjavaapp  mrunal0712/java-web-app:v2 "
            }
        }
        
        stage (' Deploy webApp in QA/Test Env') {
            steps {
                
                sshagent(['QAT_ENV_SSH_CRED']) {
                      // some block
                      sh "ssh -o StrictHostKeyChecking=no ec2-user@15.206.92.64 sudo docker rm -f myjavaapp"
                      sh "ssh ec2-user@15.206.92.64 sudo docker run -d  -p 8080:8080  --name myjavaapp  mrunal0712/java-web-app:v2"
                } 
            }
        }
        
        /*stage('QAT Test') {
            steps {
                script {
                    sh 'set +x; curl --silent http://15.206.92.64:8080/java-web-app/ | grep India'
                        }               
                    }
                }
*/

        
        stage('approval') {
            steps {
                input (message: "Release to Production ... ? ")
            }
        }
        
        stage (' Deploy webApp in Production Env') {
            steps {
                
                sshagent(['QAT_ENV_SSH_CRED']) {
                      // some block
                      sh "ssh -o StrictHostKeyChecking=no ec2-user@13.201.9.164 sudo docker rm -f myjavaapp"
                      sh "ssh ec2-user@13.201.9.164 sudo docker run -d  -p 8080:8080  --name myjavaapp  mrunal0712/java-web-app:v2"
                } 
            }
        }
        
        
        
        
        
    }
    
    
    
    
    
}
