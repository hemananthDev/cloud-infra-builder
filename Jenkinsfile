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
          
          // Create minimal inventory file
          writeFile file: 'inventory.ini', text: """
          [all]
          ${ip} ansible_user=ubuntu ansible_ssh_private_key_file=.ssh_key
          """
          
          // Create ansible.cfg for better control
          writeFile file: 'ansible.cfg', text: """
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
            cp "$SSH_KEY" .ssh_key
            chmod 600 .ssh_key .ssh_key
            
            # Run playbook with verbose output
            ansible-playbook install.yml -vv
          '''
        }
      }
    }
  }

  post {
    always {
      sh '''
        # Securely remove temporary files
        rm -f .ssh_key inventory.ini ansible.cfg
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