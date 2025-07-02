pipeline {
    agent any

    environment {
        SSH_KEY = credentials('project-key')  // Your Jenkins credential ID
    }

    stages {
        stage('Init') {
            steps {
                echo "✅ Control node is ready."
            }
        }
    }
}
