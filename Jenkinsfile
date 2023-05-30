pipeline {
    agent any
    tools {
        maven 'maven3'
    }
    stages {
        stage("build maven"){
            steps {
                sh 'mvn clean install'
            }
        }
        stage("test"){
            steps{
                sh 'mvn test'
            }
        }
        stage("deploy to tomcat"){
            steps{
                sshagent(['tomcat-privatekey']) {
                    sh "scp -o StrictHostKeyChecking=no webapp/target/petclinic.war ubuntu@3.109.3.26:/opt/tomcat/webapps"
}
            }
        }
    }
}
