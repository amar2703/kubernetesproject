pipeline {
    agent any

    stages {
        stage('PUll') {
            steps {
                git 'https://github.com/cloudjankari/kubernetesproject.git'
            }
        }
        
        stage("Build") {
            steps {
                sh 'docker image build -t $JOB_NAME:v1.$BUILD_ID .'
                sh 'docker image tag $JOB_NAME:v1.$BUILD_ID sd171991/$JOB_NAME:v1.$BUILD_ID'
                sh 'docker image tag $JOB_NAME:v1.$BUILD_ID sd171991/$JOB_NAME:latest'
                
            }
        }
        
        stage("Push") {
            steps {
                withCredentials([string(credentialsId: 'mobilehubpassword', variable: 'mobilehubpassword')]) {
    // some block
    sh 'docker login -u sd171991 -p ${mobilehubpassword}'
    sh 'docker image push sd171991/$JOB_NAME:v1.$BUILD_ID'
    sh 'docker image push sd171991/$JOB_NAME:latest'
    sh 'docker image rmi $JOB_NAME:v1.$BUILD_ID sd171991/$JOB_NAME:v1.$BUILD_ID sd171991/$JOB_NAME:latest'
}
            }
        }
        
        stage("Deployment") {
            steps {
             sshagent(['hostpassword']) {
    // some block
                 
    sh "ssh -o StrictHostKeyChecking=no ec2-user@172.31.42.54 'docker container rm -f cloudknowledges'" 
    sh "ssh -o StricHostKeyChecking=no ec2-user@172.31.42.54 'docker image rmi sd171991/demo-project'"
    sh "ssh -o StrictHostKeyChecking=no ec2-user@172.31.42.54 docker container run -d -p 8000:80 --name cloudknowledges sd171991/demo-project:latest"
    
}
        }
        }
    }
}
