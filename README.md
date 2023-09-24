# Code-Shield
Source Code Security Monitoring Tool




pipeline {
    agent any
    
    tools {
        nodejs 'node'
    }

    stages {
        stage('Step 1: Checkout') {
            steps {
                // Checkout the public Git repository
                checkout([$class: 'GitSCM', branches: [[name: 'master']], 
                    userRemoteConfigs: [[url: 'https://github.com/adrianhajdin/project_chat_application']]])
            }
        }
        stage('Step 2: Move to client folder') {
            steps {
                bat 'cd client'
            }
        }
        stage('Step 3: npm install') {
            steps {
                bat 'npm install'
            }
        }
        stage('Step 4: SonarQube analysis') {
            steps {
                script {
                    def scannerHome = tool 'sonarqube';
                    withSonarQubeEnv('My SonarQube Server') { // If you have configured more than one global server connection, you can specify its name
                        bat "${scannerHome}/bin/sonar-scanner"
                    }
                }
            }
        }
        stage('Step 5: npm audit') {
            steps {
                bat 'npm audit report --json'
            }
        }
    }
}
