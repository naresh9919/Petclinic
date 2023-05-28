pipeline {
    agent any 
    
    tools{
        jdk 'jdk11'
        maven 'maven3'
    }

    environment {
        SCANNER_HOME=tool 'sonar-scanner'
        NEXUS_URL = 'http://65.0.139.190:8081/'
        NEXUS_USERNAME = Nexus-cred('nexus-username')
        NEXUS_PASSWORD = Nexus-cred('nexus-password')

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
        
        stage("Upload jar to Nexus"){
            steps{
                nexusArtifactUploader artifacts: [
                    [
                        artifactId: 'spring-framework-petclinic', 
                        classifier: '', 
                        file: 'target/Petclinic.war', 
                        type: 'war'
                    ]    
                ], 
                credentialsId: 'Nexus-cred', 
                groupId: 'org.springframework.samples', 
                nexusUrl: '172.31.1.251:8081', 
                nexusVersion: 'nexus3', 
                protocol: 'http', 
                repository: 'http://65.0.139.190:8081/repository/petclinic/', 
                version: '5.3.13'
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
                     withDockerRegistry(credentialsId: 'dockerhub-cred', toolName: 'docker') {
                        
                        sh "docker tag image1 nareshbabu1991/petclinic:latest "
                        sh "docker push nareshbabu1991/petclinic:latest "
                    }
                }
            }
        }
    }
}
