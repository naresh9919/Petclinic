pipeline {
    agent any 
    
    tools{
        jdk 'jdk11'
        maven 'maven3'
    }
    
    environment {
        SCANNER_HOME=tool 'sonar-scanner'
    }
    
    stages{
        
        stage("Git Checkout"){
            steps{
                git branch: 'main', changelog: false, poll: false, url: 'https://github.com/naresh9919/secretsanta-generator.git'
            }
        }
        
        stage("Compile"){
            steps{
                sh "mvn clean compile"
            }
        }
        
         stage("Test Cases"){
            steps{
                sh "mvn test"
            }
        }
        
        stage("Sonarqube Analysis "){
            steps{
                withSonarQubeEnv('sonar-server') {
                    sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=secretsanta \
                    -Dsonar.java.binaries=. \
                    -Dsonar.projectKey=secretsanta '''
    
                }
            }
        }
        
         stage("Build"){
            steps{
                sh " mvn clean install"
            }
        }
        stage('Build docker image'){
            steps{
                script{
                    sh 'docker build -t nareshbabu1991/petclinic .'
                }
            }
        }
    }
}
