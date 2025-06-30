pipeline {
    agent any

    environment {
        SSH_KEY = credentials('project-key')  // Your SSH key stored in Jenkins
    }

    stages {
        stage('Set up SSH Key') {
            steps {
                sh '''
                mkdir -p ~/.ssh
                echo "$SSH_KEY" > ~/.ssh/id_rsa
                chmod 600 ~/.ssh/id_rsa
                '''
            }
        }

        stage('Terraform Init & Apply') {
            steps {
                dir('upload/terraform') {
                    sh '''
                    terraform init
                    terraform apply -auto-approve > tf_output.txt
                    terraform output -raw web_ip > ../ansible/instance_ip.txt
                    '''
                }
            }
        }

        stage('Generate Ansible Inventory') {
            steps {
                sh '''
                IP=$(cat upload/ansible/instance_ip.txt)
                echo "[web]" > upload/ansible/inventory.ini
                echo "$IP ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/id_rsa" >> upload/ansible/inventory.ini
                '''
            }
        }

        stage('Run Ansible Playbook') {
            steps {
                dir('upload/ansible') {
                    sh 'ansible-playbook -i inventory.ini main.yml'
                }
            }
        }

        stage('Done') {
            steps {
                echo "Terraform & Ansible automation complete!"
            }
        }
    }
}
