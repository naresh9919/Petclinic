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

        stage("Build"){
            steps{
                sh " mvn clean install"
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

        stage('Upload artifact to Nexus') {
            steps {
                script{
                    def readpomVersion = readMavenPom file: 'pom.xml'
                    nexusArtifactUploader artifacts: 
                    [
                        [
                            artifactId: 'spring-framework-petclinic', 
                            classifier: '', 
                            file: 'target/petclinic.war', 
                            type: 'war'
                        ]
                    ], 
                    credentialsId: 'Nexus-cred', 
                    groupId: 'org.springframework.samples', 
                    nexusUrl: '3.109.200.51:8081', 
                    nexusVersion: 'nexus3', 
                    protocol: 'http', 
                    repository: 'petclinic-release', 
                    version: "${readpomVersion.version}"
                }
            }
        }

        stage('Build docker image'){
            steps{
                script{
                    sh 'docker build -t petclinic .'
                }
            }
        }

        stage("Docker Build & Push"){
            steps{
                script{
                     withDockerRegistry(credentialsId: 'dockerhub-cred', toolName: 'docker') {
                        
                        sh "docker tag petclinic nareshbabu1991/petclinic:latest "
                        sh "docker push nareshbabu1991/petclinic:latest "
                    }
                }
            }
        }

        stage('trivy'){
            steps{
                script{
                    sh 'trivy image --severity HIGH,CRITICAL nareshbabu1991/petclinic:latest'
                }
            }
        }

        stage("deploy to tomcat"){
            steps{
                sshagent(['tomcat-privatekey']) {
                    sh "scp -o StrictHostKeyChecking=no target/petclinic.war ubuntu@3.110.155.16:/opt/tomcat/webapps"
                }
            }
        }
    }
}
