pipeline {
    agent any

    tools {
        jdk 'jdk11'
        maven 'maven3'
    }

    stages {
        stage ('checkout SCM'){
            steps {
                git branch: 'main', changelog: false, poll: false, url: 'https://github.com/naresh9919/Petclinic.git'
            }
        }

        stage ('maven Build'){
            steps {
                sh 'mvn clean install'
            }
        }

        stage ('Ansible playbook execution'){
            steps {
                script {
                    ansiblePlaybook credentialsId: 'Ansible_docker_cred', installation: 'ansible', inventory: 'hosts', playbook: 'petclinic.yml'
                }
            }
        }
    }
}
