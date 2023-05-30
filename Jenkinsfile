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
        stage("static code analasis"){
            steps {
                withSonarQubeEnv("sonar-server"){
                    sh "mvn sonar:sonar"
                }
            }
            post {
                success {
                    echo 'Archiving the artifacts'
                    archiveArtifacts artifacts: '**/target/*.war'
                }
            }
        }
        stage ('Deployments'){
            parallel {
                stage ("Deploy to Staging"){
                    steps {
                        deploy adapters: [tomcat7(credentialsId: 'tomcat-cred', path: '', url: 'http://13.233.36.155:8080/')], contextPath: null, war: '**/*.war'
                    }
                }
            }
        }
    }
}
