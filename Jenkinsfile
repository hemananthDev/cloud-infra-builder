pipeline {
  agent any
  options {
    timeout(time: 30, unit: 'MINUTES')
    disableConcurrentBuilds()
  }

  stages {
    stage('Prepare Environment') {
      steps {
        script {
          // Read IP from Terraform output
          def ip = readFile('instance_ip.txt').trim()
          
          // Create inventory file in ansible directory
          writeFile file: 'ansible/inventory.ini', text: """
          [all]
          ${ip} ansible_user=ubuntu ansible_ssh_private_key_file=.ssh_key
          """
          
          // Create ansible.cfg in ansible directory
          writeFile file: 'ansible/ansible.cfg', text: """
          [defaults]
          host_key_checking = False
          inventory = inventory.ini
          remote_user = ubuntu
          private_key_file = .ssh_key
          """
        }
      }
    }

    stage('Run Ansible Playbook') {
      environment {
        ANSIBLE_FORCE_COLOR = 'true'
      }
      steps {
        withCredentials([sshUserPrivateKey(
          credentialsId: 'EKS_SSH_Key',
          keyFileVariable: 'SSH_KEY'
        )]) {
          sh '''
            # Secure key handling
            cp "$SSH_KEY" ansible/.ssh_key
            chmod 600 ansible/.ssh_key
            
            # Change to ansible directory and run playbook
            cd ansible
            ansible-playbook install.yml -vv
          '''
        }
      }
    }
  }

  post {
    always {
      sh '''
        # Securely remove temporary files from ansible directory
        rm -f ansible/.ssh_key ansible/inventory.ini ansible/ansible.cfg
      '''
    }
    success {
      script {
        def ip = readFile('instance_ip.txt').trim()
        echo "✅ Deployment Successful!"
        echo "Access Jenkins at: http://${ip}:8080"
      }
    }
    failure {
      echo "❌ Deployment Failed - Check logs for details"
    }
  }
}