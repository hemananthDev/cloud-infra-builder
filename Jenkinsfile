pipeline {
  agent any

  stages {
    stage('Generate Inventory File') {
      steps {
        script {
          def ip = readFile('instance_ip.txt').trim()
          def inventory = "[all]\n${ip} ansible_user=ubuntu ansible_ssh_private_key_file=.ssh_key"
          writeFile file: 'inventory.ini', text: inventory
          echo "✅ Inventory file created with IP: ${ip}"
        }
      }
    }

    stage('Run Ansible Playbook') {
      steps {
        withEnv(["ANSIBLE_HOST_KEY_CHECKING=False"]) {
          withCredentials([sshUserPrivateKey(credentialsId: 'project-key', keyFileVariable: 'KEYFILE')]) {
            sh '''
              cp $KEYFILE .ssh_key
              chmod 600 .ssh_key
              ansible-playbook -i inventory.ini ansible/install.yml
            '''
          }
        }
      }
    }
  }
}
