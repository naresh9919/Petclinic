pipeline {
    agent any 
    
    tools{
        jdk 'jdk11'
        maven 'maven3'
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
        stage('Upload artifact to Nexus') {
            steps {
                script{
                    def readpomVersion = readMavenPom file: 'pom.xml'

                    def nexusRepo = readpomVersion.version.endsWith("SNAPSHOT") ? "petclinic-snapshot" : "petclinic-release"
                    
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
                    nexusUrl: '65.0.73.52:8081', 
                    nexusVersion: 'nexus3', 
                    protocol: 'http', 
                    repository: nexusRepo, 
                    version: "${readpomVersion.version}"
                }
            }
        }
    }
}
