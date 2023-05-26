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
        
        stage("Cleanup Workspace"){
            steps{
                cleanWs()
            }
        }
        
        stage("Git Checkout"){
            steps{
                git branch: 'main', changelog: false, poll: false, url: 'https://github.com/naresh9919/Petclinic.git'
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
                    sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=petclinc \
                    -Dsonar.java.binaries=. \
                    -Dsonar.projectKey=petclinic '''
    
                }
            }
        }

        stage("OWASP Dependency Check"){
            steps{
                dependencyCheck additionalArguments: '--scan ./ ', odcInstallation: 'DP-Check'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
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

        stage("Docker Build & Push"){
            steps{
                script{
                   withDockerRegistry(credentialsId: '26c0d1c5-f388-463e-8f6d-a4a83fb9fa3c', toolName: 'docker') {
                        
                        sh "docker tag image1 nareshbabu1991/pet-clinic123:latest "
                        sh "docker push nareshbabu1991/pet-clinic123:latest "
                    }
                }
            }
        }
    }
}
