pipeline {
    agent any
    stages {
        stage('Preparar') {
            steps {
                script {
                    echo 'Preparando el entorno...'
                }
            }
        }
        stage('Clonar Repositorio') {
            steps {
                git branch: 'main', url: 'https://github.com/MartinM-17/cicd-pipeline-jenkins-ansible-nginx-apache.git'
            }
        }
        stage('Ejecutar Ansible') {
            steps {
                script {
                    sh 'ansible-playbook -i hosts.ini deploy.yml'
                }
            }
        }
    }
}
