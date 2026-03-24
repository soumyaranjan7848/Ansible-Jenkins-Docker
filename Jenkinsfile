pipeline {
    agent any

    environment {
        ANSIBLE_HOST_KEY_CHECKING = "False"
    }

    stages {

        stage('Clone Repository') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/soumyaranjan7848/Ansible-Jenkins-Docker.git'
            }
        }

        stage('Prepare Files') {
            steps {
                sh '''
                cp index.html ansible/roles/nginx/files/index.html
                '''
            }
        }

        stage('Deploy via Ansible') {
            steps {
                sh '''
                cd ansible
                ansible-playbook -i inventory playbook.yml
                '''
            }
        }
    }
}
