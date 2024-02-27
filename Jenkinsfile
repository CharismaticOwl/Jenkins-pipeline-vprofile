pipeline{
    agent any

    stages{
        stage('Fetch the code'){
            steps{
                git branch:'ci-jenkins', url:'https://github.com/CharismaticOwl/Jenkins-pipeline-vprofile.git'
            }
        }

        stage('CodeArtifact authorization token'){
            steps{
                sh 'export CODEARTIFACT_AUTH_TOKEN=`aws codeartifact get-authorization-token --domain vprofile --domain-owner 367065853931 --region ap-south-1 --query authorizationToken --output text`'
                sh 'echo $CODEARTIFACT_AUTH_TOKEN'
            }
        }

        stage('Unit Test'){
            steps{
                sh 'mvn test'
            }
        }
    }
}