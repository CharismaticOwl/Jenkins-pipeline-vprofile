pipeline{
    agent any

    environment{
        CODEARTIFACT_AUTH_TOKEN = ''
    }

    stages{
        stage('Fetch the code'){
            steps{
                git branch:'ci-jenkins', url:'https://github.com/CharismaticOwl/Jenkins-pipeline-vprofile.git'
            }
        }

        stage('CodeArtifact authorization token'){
            steps{
                script{
                    def authToken = sh(script: 'aws codeartifact get-authorization-token --domain vprofile --domain-owner 367065853931 --region ap-south-1 --query authorizationToken --output text', returnStdout: true).trim()
                    CODEARTIFACT_AUTH_TOKEN = authToken
                }
            }
        }

        stage('Unit Test'){
            steps{
                sh 'mvn test'
            }
        }

        stage('Integration Test'){
            steps{
                sh 'mvn verify -Dskiptests'
            }
        }

        stage('Code analysis with Checkstyle'){
            steps{
                sh 'mvn checkstyle:checkstyle'
            }
            post{
                success{
                    echo 'Generated Analysis Result'
                }
            }
        }

        stage('SonarQube analysis') {
            environment {
                scannerHome = tool 'sonarscanner'
            }
            steps{
                withSonarQubeEnv('SonarCloud') { 
                    sh '''${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=sonar-vprofile-key \
                   -Dsonar.organization=sonar-vprofile \
                   -Dsonar.projectVersion=1.0 \
                   -Dsonar.sources=src/ \
                   -Dsonar.java.binaries=target/test-classes/com/visualpathit/account/controllerTest/ \
                   -Dsonar.junit.reportsPath=target/surefire-reports/ \
                   -Dsonar.jacoco.reportsPath=target/jacoco.exec \
                   -Dsonar.java.checkstyle.reportPaths=target/checkstyle-result.xml'''
                }
            }
        }

        stage('Build'){
            steps{

                sh "sed -i 's/env.CODEARTIFACT_AUTH_TOKEN/${CODEARTIFACT_AUTH_TOKEN}/g' settings.xml"
                sh 'mvn clean install -s settings.xml -Dskiptests'
            }
            post{
                success{
                    echo 'Now Archiving'
                    archiveArtifacts artifacts: '**/target/*.war'
                }
            }
        }
    }
}