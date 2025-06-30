pipeline {
    agent any

    environment {
        SSH_KEY = credentials('project-key')  // This matches your Jenkins credential ID
    }

    stages {
        stage('Clone Repo') {
            steps {
                echo "Cloned repo successfully!"
            }
        }

        stage('Setup SSH Key') {
            steps {
                sh '''
                mkdir -p ~/.ssh
                echo "$SSH_KEY" > ~/.ssh/id_rsa
                chmod 600 ~/.ssh/id_rsa
                '''
                echo "SSH key set up!"
            }
        }

        stage('Done') {
            steps {
                echo "Pipeline ran successfully!"
            }
        }
    }
}
