pipeline{
    agent any
    tools{
        maven 'maven3'
    }
     environment {
    DOCKERHUB_CREDENTIALS = credentials('nn-dockerhub')
    }
    
    stages{
        stage('checkout the code'){
            steps{
                git url:'https://github.com/Narian318/samplejavademo.git', branch: 'master'
            }
        }
         stage('build the code'){
            steps{
                sh 'mvn clean package'
            }
        }
         stage('Docker Build') {
            steps {
                sh "docker build . -t narian318/nn-mvn-dckr-tomcat:${BUILD_NUMBER}"
            }
        }
        stage('Login') {
            steps {
        sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
            }
        }
        stage('Push') {
            steps {
        sh 'docker push narian318/nn-mvn-dckr-tomcat:${BUILD_NUMBER}'
            }
        }
        stage('Docker Deploy') {
            steps {
                sshagent(['nn-tomcat-ssh']) {
                    sh "ssh -o StrictHostKeyChecking=no ec2-user@54.237.150.22 docker rm -f nn-tomcat-container"
                    sh "ssh ec2-user@54.237.150.22 docker run -d -p 8080:8080 --name nn-tomcat-container narian318/nn-mvn-dckr-tomcat:${BUILD_NUMBER}"
                }
            }
        }
    }
   }
