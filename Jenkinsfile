pipeline {
    agent any

    environment {
        SSH_KEY = credentials('project-key') // 👈 uses your existing Jenkins credential
    }

    stages {
        stage('Generate Inventory File') {
            steps {
                script {
                    def ip = readFile('instance_ip.txt').trim()
                    def inventory = "[all]\n${ip} ansible_user=ubuntu ansible_ssh_private_key_file=${env.SSH_KEY}\n"
                    writeFile file: 'inventory.ini', text: inventory
                }
                echo "✅ Inventory file created with IP from instance_ip.txt"
            }
        }

        stage('Run Ansible Playbook') {
            steps {
                sh '''
                    ansible-playbook -i inventory.ini ansible/install.yml
                '''
            }
        }
    }
}
