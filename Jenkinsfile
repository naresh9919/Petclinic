pipeline {
    agent any

    tools {
        jdk 'jdk11'
        maven 'maven3'
    }

    stages {
        stage ('checkout SCM'){
            steps {
                git branch: 'master', changelog: false, poll: false, url: 'https://github.com/naresh9919/amazon-eks-jenkins-terraform-aj7.git'
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
                ansiblePlaybook credentialsId: 'Ansible', 
                disableHostKeyChecking: true, 
                installation: 'Ansible', 
                inventory: 'hosts', 
                playbook: 'docker-ansible.yml'
            }
        }
    }
}
